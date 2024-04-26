<script setup>
    //Problema 1: 
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
let deltaLlegada = 45;
let deltaFinServicio = 40;
let hora_actual = ahora;
let prox_llegada = addMinute(hora_actual, 5);
let prox_fin_servicio = addMinute(hora_actual, 3);
let atendidos = 0;
let ps = true;
let q = 3;
let i = 0;
const datosTabla = ref([]);
const estadoInicial = ref({
    hora_actual: format(hora_actual,{time:"medium"}),
    prox_llegada: format(prox_llegada,{time:"medium"}),
    prox_fin_servicio: format(prox_fin_servicio,{time:"medium"}),
    atendidos: atendidos,
    q: q,
    ps: ps
});


const ejecutarSimulaion = () => {

    console.log('Hora Actual || Proxima llegada || Proximo fin de servicio || Cola ||  Puesto de servicio');
    console.log(`${format(hora_actual,{time:"medium"})}  ||  ${format(prox_llegada,{time:"medium"})}  ||  ${format(prox_fin_servicio,{time:"medium"})} || ${q} || ${ps}`);
    datosTabla.value.push(estadoInicial.value);
    while(i <= 10){
        if(isBefore(prox_llegada,prox_fin_servicio) || isEqual(prox_llegada,prox_fin_servicio)){

            hora_actual = prox_llegada;
            if(ps == false){
                ps = true;
                prox_fin_servicio = addSecond(hora_actual, deltaFinServicio);
            }else{
                q += 1;
            }
            
            prox_llegada = addSecond(hora_actual, deltaLlegada);
        }else{

            hora_actual = prox_fin_servicio;

            ps = false;
            atendidos += 1;
            if(q > 0){
                q -= 1;
                ps = true
                prox_fin_servicio = addSecond(hora_actual, deltaFinServicio);
            }else{
                ps = false
                prox_fin_servicio = dayEnd(prox_fin_servicio)
            }
        }
        i++;
        console.log('-----------------------------------');
        estadoInicial.value = {
            hora_actual: format(hora_actual,{time:"medium"}),
            prox_llegada: format(prox_llegada,{time:"medium"}),
            prox_fin_servicio: format(prox_fin_servicio,{time:"medium"}),
            atendidos: atendidos,
            q: q,
            ps: ps
        }

        datosTabla.value.push(estadoInicial.value)

        console.log(`${format(hora_actual,{time:"medium"})}  ||  ${format(prox_llegada,{time:"medium"})}  ||  ${format(prox_fin_servicio,{time:"medium"})} || ${q} || ${ps}`);
    }
}
</script>

<template>
    <h1>Ejercicio 1</h1>
    <button @click="ejecutarSimulaion" class="btn btn-primary">Ejecutar</button>
    <div
        class="table-responsive mt-3"
    >
        <table
            class="table table-striped table-sm"
        >
            <thead>
                <tr>
                <th>Hora Actual</th>
                <th>Proxima Llegada</th>
                <th>Proximo Fin de Servicio</th>
                <th>Cola</th>
                <th>Puesto de Servicio</th>
                <th>Atendidos</th>

            </tr>
            </thead>
            <tbody>
            <tr v-for="item in datosTabla">
                <td>{{item.hora_actual}}</td>
                <td>{{item.prox_llegada}}</td>
                <td>{{item.prox_fin_servicio}}</td>
                <td>{{item.q}}</td>
                <td>{{item.ps ? 'Ocupado' : 'Libre'}}</td>
                <td>{{ item.atendidos }}</td>
            </tr>
        </tbody>
        </table>
    </div>
    
</template>
