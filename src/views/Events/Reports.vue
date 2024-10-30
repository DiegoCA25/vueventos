<script setup>
import { ref, onMounted } from 'vue';
import { useAuthStore } from '../../stores/auth';
import SelectInput from '../../components/SelectInput.vue';
import DataTable from 'datatables.net-vue3';
import 'datatables.net-dt/css/dataTables.dataTables.css';

import ButtonsHtml5 from 'datatables.net-buttons/js/buttons.html5';
import 'datatables.net-buttons/js/buttons.print';
import 'datatables.net-responsive-dt';
import 'datatables.net-responsive-dt/css/responsive.dataTables.min.css';
import JsZip from 'jszip';
import pdfmake from 'pdfmake/build/pdfmake';
import * as pdfFonts from 'pdfmake/build/vfs_fonts';

pdfmake.vfs = pdfFonts.pdfMake ? pdfFonts.pdfMake.vfs : pdfmake.vfs;
window.JSZip = JsZip;
DataTable.use(ButtonsHtml5);

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] = 'Bearer ' + authStore.authToken;


const cities = ref([]);
const events = ref([]);

const columns1 = ref([]);
const columns2 = ref([]);
const buttons1 = ref([]);
const buttons2 = ref([]);
const report = ref('1');
const types = ref([
    { id: '1', name: 'Cities' },
    { id: '2', name: 'Events' },
]);

onMounted(async () => {
        const g = await axios.get('/api/cities');
        cities.value = g.data;
        const b = await axios.get('/api/eventsall');
        events.value = b.data;
    });

columns1.value = [
    { data: null, render:function (data, type, row, meta)
         {return (meta.row + 1)}},
    { data: 'name' },
    { data: 'datei' },
    { data: 'datef' },
    { data: 'promo' },
    { data: 'city' },
];

columns2.value = [
    { data: null, render:function (data, type, row, meta)
        {return (meta.row + 1)}},
    { data: 'name' },
];

buttons1.value = [
    {
        title: 'Events',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Events',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Events',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Events',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];

buttons2.value = [
    {
        title: 'Cities',
        extend: 'excelHtml5',
        text: '<i class="fa-solid fa-file-excel"></i> Excel',
        className: 'btn btn-success',
    },
    {
        title: 'Cities',
        extend: 'pdfHtml5',
        text: '<i class="fa-solid fa-file-pdf"></i> PDF',
        className: 'btn btn-danger',
    },
    {
        title: 'Cities',
        extend: 'print',
        text: '<i class="fa-solid fa-print"></i> Print',
        className: 'btn btn-dark',
    },
    {
        title: 'Cities',
        extend: 'copy',
        text: '<i class="fa-solid fa-copy"></i> Copy',
        className: 'btn btn-secondary',
    },
];
</script>

<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Report:
            <SelectInput id="rep" class="mt-1" v-model="report" :options="types" />
        </div>
    </div>
    <div class="row" v-if="report == '1'">
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable
                    :data="events"
                    :columns="columns1"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons1 }"
                >
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>NAME</th>
                            <th>START</th>
                            <th>END</th>
                            <th>PROMO</th>
                            <th>CITY</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
    <div class="row" v-else>
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable
                    :data="cities"
                    :columns="columns2"
                    class="table table-striped"
                    :options="{ responsive: true, autoWidth: false, dom: 'Bfrtip', buttons: buttons2 }"
                >
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>CITIES</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
</template>