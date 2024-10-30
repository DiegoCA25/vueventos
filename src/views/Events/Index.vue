<script setup>
import {ref,onMounted, nextTick} from 'vue';
import { confirmation, sendRequest } from '../../functions';
import { useAuthStore } from '../../stores/auth';
import Modal from '../../components/Modal.vue';
import SelectInput from '../../components/SelectInput.vue';
import Paginate from 'vuejs-paginate-next';
const authStore = useAuthStore();
axios.defaults.headers.common ['Authorization'] = 'Bearer '+authStore.authToken;

onMounted( ()=> { getCities(), getEvents(1) });
const cities = ref ([]);
const events = ref([]);
const load = ref(false);
const rows = ref(0);
const form = ref({
    name:'', datei:'', datef:'', promo:'', city_id:''
});
const title = ref('');
const nameInput = ref('');
const operation = ref(1);
const id = ref('');
const close = ref([]);


const getCities = async () =>{
    await axios.get('/api/cities').then(
    response =>(cities.value = response.data));
    load.value = true; 
}
const getEvents = async (page) =>{
    await axios.get('/api/events?page='+page).then(
    response =>(
        events.value = response.data,
        rows.value = response.data.last_page
    ));
    load.value = true; 
}
const deleteEvents = (id,name) =>{
    confirmation(name,('/api/events/'+id), '/events');
}
const openModal = (op, name, datei, datef, promo, city, event) =>{
    clear();
    setTimeout( ()=> nextTick(nameInput.value.focus()),600);
    operation.value=op;
    id.value = event;
    if(op == 1){
        title.value = 'Create Event'
    }
    else{
        title.value = 'Create Event'
        form.value.name = name;
        form.value.datei = datei;
        form.value.datef = datef; 
        form.value.promo = promo;
        form.value.city_id = city; 
    }
}
const clear = ()=>{
    form.value.name = '';
    form.value.datei = '';
    form.value.datef = ''; 
    form.value.promo = '';
    form.value.city_id = '';
}
const save = async () =>{
    let res;
    if(operation.value ==1){
        res= await sendRequest('POST',form.value,'/api/events/','');
        if(res == true){
            clear();
            nextTick( ()=> nameInput.value.focus());
            getEvents(1);
        }   
    }
    else{
        res= await sendRequest('PUT',form.value,('/api/events/'+id.value),'');
        if(res == true){
            nextTick( ()=> close.value.click());
            getEvents(1);
        }  
    }
}
</script>
<template>
    <div class="row mt-5">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-dark" data-bs-toggle="modal"
                data-bs-target="#modal" @click="$event => openModal(1)">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </button>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-10 offset-md-1">
            <div class="card border border-white text-center" v-if="!load">
            <div class="card-body">
                <img src="/public/Loading.gif" class="img-fluid">
            </div>
            </div>
            <div class="table-responsive" v-else>
            <table class="table table-bordered table-hover">
                <thead>
                    <tr>
                        <th>#</th>
                        <th>EVENTO</th>
                        <th>INICIO</th>
                        <th>FIN</th>
                        <th>PROMO</th>
                        <th>CIUDAD</th>
                        <th></th>
                        <th></th>
                    </tr>
                </thead>
                <tbody class="table-group-divider">
                    <tr v-for="eve,i in events.data" :key="eve.id">
                        <td>{{ (i+1) }}</td>
                        <td>{{ eve.name }}</td>
                        <td>{{ eve.datei }}</td>
                        <td>{{ eve.datef }}</td>
                        <td>{{ eve.promo }}</td>
                        <td>{{ eve.city }}</td>
                        <td>
                           <button class="btn btn-warning" data-bs-toggle="modal"
                           data-bs-target="#modal" @click="$event => openModal(2,eve.name, eve.datei, eve.datef, eve.promo, eve.city)">
                            <i class="fa-solid fa-edit"></i>
                           </button>
                        </td>
                        <td>
                            <button class="btn btn-danger" @click="$event => deleteEvents(eve.id,eve.name)">
                                <i class="fa-solid fa-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>  
            <Paginate :page-count="rows" :click-handler="getEvents"
            :prev-next="'prev'" next-text="Next" :conatiner-class="Paginate" >

            </Paginate>
            </div>
        </div>
    </div>
    <Modal id="modal" :title="title">
        <div class="modal-body">
            <form @submit.prevent="save">
                <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-star"></i>
                            </span>
                            <input autofocus type="text" v-model="form.name"
                            placeholder="Name" class="form-control" required ref="nameInput">
                 </div>
                 <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-calendar-days"></i>
                            </span>
                            <input autofocus type="date" v-model="form.datei"
                            placeholder="Inicio" class="form-control" required >
                 </div>
                 <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-calendar-days"></i>
                            </span>
                            <input autofocus type="date" v-model="form.datef"
                            placeholder="Fin" class="form-control" required >
                 </div>
                 <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-date"></i>
                            </span>
                            <input autofocus type="text" v-model="form.promo"
                            placeholder="Promocional" class="form-control" >
                 </div>
                 <div class="input-group mb-3">
          <span class="input-group-text"><i class="fa-solid fa-building"></i></span>
          <SelectInput v-model="form.city_id" :options="cities" class="form-select" ></SelectInput>
        </div>
        <div class="d-grid col-10 mx-auto"> 
                            <button class="btn btn-success">
                                <i class="fa-solid fa-save"></i> Save</button>   
                        </div>
            </form>
        </div>
    </Modal>
</template>