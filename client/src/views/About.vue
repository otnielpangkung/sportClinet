<template>
	<div class="main m-2">
		<div class="olahragas">
			<div class="row"></div>
			<div class="row">
				<b-table striped hover :items="olahragas" :fields="fieldsOlahraga">
					<template #cell(jumlah)="row">{{
						formatUang(totalJumlah(row.item.Kegiatans))
					}}</template>
					<template #cell(target)="row">{{ formatUang(row.value) }}</template>
					<template #cell(kurang)="row">{{
						formatUang(kurang(totalJumlah(row.item.Kegiatans), row.item.target))
					}}</template>
					<template #cell(kegiatan)="row">{{
						row.item.Kegiatans.length
					}}</template>
				</b-table>
			</div>

			<div class="row mb-2">
				<div class="input-group col-sm">
					<label class="my-1 mr-2" for="selectedMonth">Periode :</label>
					<vue-monthly-picker v-model="selectedMonth"></vue-monthly-picker>
					<b-button @click="clearTanggal()">Clear</b-button>
				</div>
			</div>
			<div class="">
				<table
					class="table table-bordered border-primary table-hover 6table-striped"
					id="relasiTable"
				>
					<thead class="table">
						<th>Nama Olahraga</th>

						<th>Total</th>
						<th>1</th>
						<th>2</th>
						<th>3</th>
						<th>4</th>
						<th>5</th>
						<th>6</th>
						<th>7</th>
						<th>8</th>
						<th>9</th>
						<th>10</th>
						<th>11</th>
						<th>12</th>
						<th>13</th>
						<th>14</th>
						<th>15</th>
						<th>16</th>
						<th>17</th>
						<th>18</th>
						<th>19</th>
						<th>20</th>
						<th>21</th>
						<th>22</th>
						<th>23</th>
						<th>24</th>
						<th>25</th>
						<th>26</th>
						<th>27</th>
						<th>28</th>
						<th>29</th>
						<th>30</th>
						<th>31</th>
					</thead>
					<tbody>
						<tr v-for="sport in this.olahragas" :key="sport.id">
							<td>{{ sport.namaOlahraga }}</td>

							<td>{{ getHarian(sport.Kegiatans, 0) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 1) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 2) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 3) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 4) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 5) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 6) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 7) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 8) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 9) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 10) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 11) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 12) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 13) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 14) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 15) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 16) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 17) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 18) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 19) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 20) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 21) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 22) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 23) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 24) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 25) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 26) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 27) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 28) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 29) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 30) }}</td>
							<td>{{ getHarian(sport.Kegiatans, 31) }}</td>
						</tr>
					</tbody>
				</table>
			</div>
		</div>
		<div class="row">
			<div class="input-group col col-lg-5">
				<label class="my-1 mr-2" for="Username">Tanggal :</label>
				<input type="date" class="form-control mr-3" v-model="startDate" /> -
				<input type="date" class="form-control ml-3" v-model="endDate" />
				<button
					id="download"
					class="btn"
					@click.prevent="tableHtmlToExcel('table')"
				>
					<i class="fa fa-download"></i> Download
				</button>
			</div>
			<b-pagination
				v-model="currentPage"
				:total-rows="filterData().length"
				:per-page="perPage"
				aria-controls="kegiatan-table"
			></b-pagination>
			<b-table
				id="kegiatan-table"
				striped
				hover
				:items="filterData()"
				:per-page="perPage"
				:current-page="currentPage"
				:fields="fieldsKegiatan"
			>
				<template #cell(action)="row"
					><b-button @click.prevent="deleteKegiatan(row.item.id)">
						Hapus
					</b-button></template
				>
			</b-table>
		</div>
	</div>
</template>

<script>
	import axios from '../API/axios';
	import Swal from 'sweetalert2';
	import VueMonthlyPicker from 'vue-monthly-picker';
	import moment from 'moment';

	export default {
		name: 'About',
		components: {
			VueMonthlyPicker,
		},
		data() {
			return {
				perPage: 20,
				currentPage: 1,
				namaOlahraga: '',
				kegiatans: [],
				target: 0,
				satuan: '',
				olahragas: [],
				endDate: '',
				startDate: '',
				selectedMonth: '',
				fieldsOlahraga: [
					{ key: 'namaOlahraga', label: 'NAMA OLAHRAGA' },
					{ key: 'target', label: 'TARGET', class: 'text-right' },
					{ key: 'satuan', label: 'Satuan' },
					{ key: 'jumlah', class: 'text-right', label: 'JUMLAH' },
					{ key: 'kurang', class: 'text-right', label: 'KURANG' },
					{ key: 'kegiatan', label: 'KEGIATAN' },
				],
				fieldsKegiatan: [
					{ key: 'tanggal', label: 'TANGGAL' },
					{ key: 'jam', label: 'WAKTU' },
					{ key: 'Olahraga.namaOlahraga', label: 'OLAHRAGA' },
					{ key: 'jumlah', label: 'JUMLAH' },
					{ key: 'keterangan', label: 'KETERANGAN' },
					{ key: 'action', label: 'HAPUS' },
				],
			};
		},
		created() {
			this.olahragaList();
			this.kegiatanList();
			this.filterData();
		},
		methods: {
			filterData() {
				let hasil = [];
				if (!this.startDate || !this.endDate) {
					hasil = this.kegiatans;
				} else {
					this.kegiatans?.map((item) => {
						let start = moment(this.startDate, 'YYYY/MM/DD') - 1;
						let end = moment(this.endDate, 'YYYY/MM/DD') + 1;
						let tanggalItem = moment(item.tanggal, 'YYYY/MM/DD');
						if (tanggalItem < end && tanggalItem > start) {
							hasil.push(item);
						}
					});
				}
				return hasil;
			},
			olahragaList() {
				return axios
					.get('/olahraga')
					.then(({ data }) => {
						this.olahragas = data;
					})
					.catch((err) => {
						console.log(err);
					});
			},
			kegiatanList() {
				return axios
					.get('/kegiatan')
					.then(({ data }) => {
						this.kegiatans = data;
					})
					.catch((err) => {
						console.log(err);
					});
			},
			addOlahraga() {
				let payload = {
					namaOlahraga: this.namaOlahraga,
					satuan: this.satuan,
					target: +this.target,
				};
				return axios({
					method: 'post',
					data: payload,
					url: '/olahraga',
				});
			},
			deleteKegiatan(id) {
				return axios.delete(`/kegiatan/${id}`).then((data) => {
					Swal.fire('Ok', 'Data anda telah dihapus', 'success');
					this.kegiatanList();
				});
			},
			clearTanggal() {
				this.selectedMonth = '';
			},
			getHarian(data, tanggal) {
				let hasil = 0;

				if (tanggal !== 0) {
					data?.map((item) => {
						const bulan = moment(item.tanggal).format('MM-YYYY');
						const periode = moment(this.selectedMonth).format('MM-YYYY');
						const tanggalItem = moment(item.tanggal).format('DD');
						if (bulan == periode && tanggal == tanggalItem) {
							hasil += item.jumlah;
						}
					});
				} else {
					data?.map((item) => {
						const bulan = moment(item.tanggal).format('MM-YYYY');
						const periode = moment(this.selectedMonth).format('MM-YYYY');

						if (bulan == periode) {
							hasil += item.jumlah;
						}
					});
				}
				if (hasil == 0) hasil = '-';
				return hasil;
			},
			formatTanggal(data) {
				let hasil = '';
				var time = moment(data).format('DD-MM-YYYY h:mm:ss');
				hasil = time.slice(0, 10);
				return hasil;
			},
			totalJumlah(data) {
				let hasil = 0;
				data?.map((el) => {
					hasil += el.jumlah;
				});
				return hasil;
			},
			kurang(real, target) {
				let hasil = 0;
				hasil = target - real;
				return hasil;
			},
			formatUang(data) {
				// console.log(data, "dataa");
				let uang = '';
				data = data.toString();
				for (let i = 0; i < data.length; i++) {
					if ((data.length - i) % 3 == 0 && i !== 0) {
						uang += `.${data[i]}`;
					} else {
						uang += data[i];
					}
				}
				return uang;
			},
		},
	};
</script>

<style></style>
