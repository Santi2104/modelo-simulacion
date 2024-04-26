<script setup>
    //Problema 2: 
    /*
    Hora de inicio de la simulacion: 08:00 hrs
    Hora de fin de la simulacion: 09:00 hrs
    Personas en la cola: 3
    Estado del servidor: 1 -> ocupado
    Tiempo de arrivo de clientes: 45 segundos
    Tiempo de fin de servicio: 40 segundos
    */
import {ref} from 'vue'
import { format,dayEnd,addMinute,addSecond,isBefore,isEqual} from "@formkit/tempo"

let ahora = new Date();
ahora.setHours(8, 0, 0, 0);
let hora_actual = ahora;
let deltaLlegada = 15;
let deltaFinServicio = 10;
let deltaDescanso = 30;
let deltaTrabajo = 60;
let prox_llegada = addSecond(hora_actual, deltaLlegada);
let prox_fin_servicio = addSecond(hora_actual,deltaFinServicio);
let prox_descanso = addSecond(hora_actual, deltaDescanso);
let prox_trabajo = dayEnd(hora_actual);
let atendidos = 0;
let ps = true;
let psStatus = true;
let q = 2;
let iteraciones = ref(3);
let cantidad_descansos = 0;
let cantidad_regresos = 0;
let fin_simulacion = ref(false);
const datosTabla = ref([]);
const estadoInicial = ref({
    hora_actual: format(hora_actual,{time:"medium"}),
    prox_llegada: format(prox_llegada,{time:"medium"}),
    prox_fin_servicio: format(prox_fin_servicio,{time:"medium"}),
    prox_descanso: format(prox_descanso,{time:"medium"}),
    prox_trabajo: format(prox_trabajo,{time:"medium"}),
    atendidos: atendidos,
    q: q,
    ps: ps,
    ps_estatus: psStatus
});

const proximo_evento = (llegada,fin,descanso,trabajo) => {

    const tiempos = [llegada,fin,descanso,trabajo];
    const proximoEvento = new Date(Math.min(...tiempos));
    if(format(llegada,{time:"medium"}) == format(proximoEvento,{time:"medium"})){
        return 1
    }
    if(format(fin,{time:"medium"}) == format(proximoEvento,{time:"medium"})){
        return 2
    }
    if(format(descanso,{time:"medium"}) == format(proximoEvento,{time:"medium"})){
        return 3
    }
    if(format(trabajo,{time:"medium"}) == format(proximoEvento,{time:"medium"})){
        return 4
    }
    
}

const evento_llegada = () => {
    hora_actual = prox_llegada;
    prox_llegada = addSecond(hora_actual, deltaLlegada);
    if(psStatus){
        if(ps){
            q++
        }else{
            ps = true
            prox_fin_servicio = addSecond(hora_actual, deltaFinServicio);
        }
    }else{
        q++
    }
}

const evento_fin_servicio = () => {
    hora_actual = prox_fin_servicio;
    ps = false;
    atendidos++;
    if(psStatus){
        if(q > 0){
            q--
            ps = true
            prox_fin_servicio = addSecond(hora_actual, deltaFinServicio);
        }else{
            ps = false
            prox_fin_servicio = dayEnd(prox_fin_servicio)
        }
    }else{
        prox_fin_servicio = addSecond(hora_actual, deltaDescanso);
    }
}

const evento_descanso = () => {
    hora_actual = prox_descanso;
    cantidad_descansos++;
    psStatus = false;
    prox_trabajo = addSecond(prox_descanso, deltaDescanso);
    prox_fin_servicio = addSecond(prox_fin_servicio, deltaDescanso);
    prox_descanso = dayEnd(prox_descanso);
}

const evento_trabajo = () => {
    cantidad_regresos++;
    hora_actual = prox_trabajo;
    psStatus = true;
    prox_descanso = addSecond(prox_trabajo, deltaTrabajo);
    prox_trabajo = dayEnd(prox_trabajo);
}

const simulacion = () =>{

    datosTabla.value.push(estadoInicial.value);
    let i = 0
    while( i < iteraciones.value){
        let op = proximo_evento(prox_llegada,prox_fin_servicio,prox_descanso,prox_trabajo);
        deltaDescanso = Math.floor(Math.random() * (30 - 1) ) + 1
        switch (op) {
            case 1:
                console.log("LLEGADA")
                evento_llegada();
                break;
            case 2:
                console.log("FIN de SERVICIO")
                evento_fin_servicio();
                break;
            case 3:
                console.log("DESCANSO")
                evento_descanso();
                break;
            case 4:
                console.log("TRABAJO")
                evento_trabajo();
                break;
            default:
                break;
        }
        estadoInicial.value = {
                hora_actual: format(hora_actual,{time:"medium"}),
                prox_llegada: format(prox_llegada,{time:"medium"}),
                prox_fin_servicio: format(prox_fin_servicio,{time:"medium"}),
                prox_descanso: format(prox_descanso,{time:"medium"}),
                prox_trabajo: format(prox_trabajo,{time:"medium"}),
                atendidos: atendidos,
                q: q,
                ps: ps,
                ps_estatus: psStatus
            }
        datosTabla.value.push(estadoInicial.value)
        i++;
    }
    fin_simulacion.value = true
}
    



</script>

<template>    
    <h1>Ejercicio 2</h1>
    <div class="row">
        <div class="col-3">
            <div class="mb-3 mt-2">
                <label for="" class="form-label">Cantidad de iteraciones</label>
                <input
                type="text"
                class="form-control"
                name="iteraciones"
                id="iteraciones"
                v-model.number="iteraciones"
                placeholder="Cantidad de iteraciones"
                />
            </div>
        </div>
    </div>
    <button @click="simulacion" class="btn btn-primary">Ejecutar</button>
    <div
        class="table-responsive mt-3"
    >
        <table
            class="table table-striped table-sm"
        >
            <thead>
                <tr>
                    <th>Iteración</th>
                    <th>Hora actual</th>
                    <th>Proxima llegada</th>
                    <th>Proximo fin de servicio</th>
                    <th>Proximo descanso</th>
                    <th>Proxima vuelta al trabajo</th>
                    <th>Cola</th>
                    <th>Puesto de servicio</th>
                    <th>Estado de servicio</th>
                    <th>Atendidos</th>
                </tr>
            </thead>
            <tbody>
            <tr v-for="(item,index) in datosTabla">
                <td>{{index + 1}}</td>
                <td>{{item.hora_actual}}</td>
                <td>{{item.prox_llegada}}</td>
                <td>{{item.prox_fin_servicio}}</td>
                <td>{{item.prox_descanso}}</td>
                <td>{{item.prox_trabajo}}</td>
                <td>{{item.q}}</td>
                <td>{{item.ps ? 'Ocupado' : 'Libre'}}</td>
                <td>{{item.ps_estatus ? 'Trabajando' : 'Descanso'}}</td>
                <td>{{ item.atendidos }}</td>
            </tr>
        </tbody>
        </table>
    </div>
    <div class="row">
        <template v-if="fin_simulacion">
            <div>
                Cantidad de descansos: {{ cantidad_descansos }}
            </div>
            <div>
                Cantidad de regresos: {{ cantidad_regresos }}
            </div>
        </template>
    </div>
</template>
