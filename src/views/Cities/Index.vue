<script setup>
import {ref,onMounted} from 'vue';
import { confirmation } from '../../functions';
import { useAuthStore } from '../../stores/auth';
const authStore = useAuthStore();
axios.defaults.headers.common ['Authorization'] = 'Bearer '+authStore.authToken;
onMounted( ()=> { getCities() });
const cities = ref ([]);
const load = ref(false);
const getCities = async () =>{
    await axios.get('/api/cities').then(
    response =>(cities.value = response.data));
    load.value = true; 
}
const deleteCity = (id,name) =>{
    confirmation(name,('/api/cities/'+id), '/cities');
}
</script>
<template>
    <div class="row mt-5">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <router-link :to="{ path:'create'}" class="btn btn-dark">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </router-link>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-6 offset-md-3">
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
                        <th>CIUDAD</th>
                        <th></th>
                        <th></th>
                    </tr>
                </thead>
                <tbody class="table-group-divider">
                    <tr v-for="cit,i in cities" :key="cit.id">
                        <td>{{ (i+1) }}</td>
                        <td>{{ cit.name }}</td>
                        <td>
                            <router-link :to="{path:'edit/'+cit.id }" class="btn btn-warning">
                                <i class="fa-solid fa-edit"></i>
                            </router-link>
                        </td>
                        <td>
                            <button class="btn btn-danger" @click="$event => deleteCity(cit.id,cit.name)">
                                <i class="fa-solid fa-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>  
            </div>
        </div>
    </div>
</template>