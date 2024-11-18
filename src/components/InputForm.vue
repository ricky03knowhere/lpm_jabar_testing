<template>
  <div class="container p-4 flex flex-col lg:flex-row">
    <div class="basis-1/2">
      <h1 class="text-2xl font-bold my-4">
        Input Data Penerima Bantuan Sosial
      </h1>
      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div class="form-control">
          <label class="label">
            <span class="label-text">Nama</span>
          </label>
          <input v-model="formData.nama" type="text" required class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">NIK</span>
          </label>
          <input v-model.number="formData.nik" type="number" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Nomor Kartu Keluarga</span>
          </label>
          <input v-model.number="formData.nomorKartuKeluarga" type="number" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Foto KTP</span>
          </label>
          <input type="file" @change="handleFileUpload('fotoKTP', $event)"
            class="file-input file-input-bordered input-info w-full max-w-md" required accept="image/*" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Foto Kartu Keluarga</span>
          </label>
          <input type="file" @change="handleFileUpload('fotoKartuKeluarga', $event)"
            class="file-input file-input-bordered input-info w-full max-w-md" required accept="image/*" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Umur</span>
          </label>
          <input v-model.number="formData.umur" type="number" min="25" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Jenis Kelamin</span>
          </label>
          <select v-model="formData.jenisKelamin" required class="select select-bordered select-info w-full max-w-md">
            <option value="">Pilih</option>
            <option value="Laki-laki">Laki-laki</option>
            <option value="Perempuan">Perempuan</option>
          </select>
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Provinsi</span>
          </label>
          <select v-model="formData.provinsi" @change="fetchAPI('kabKota')" required
            class="select select-bordered select-info w-full max-w-md">
            <option value="">Pilih Provinsi</option>
            <option v-for="provinsi in dataWilayah.provinsi" :key="provinsi.id" :value="provinsi.id">{{ provinsi.name }}
            </option>
          </select>
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Kab/Kota</span>
          </label>
          <select v-model="formData.kabKota" @change="fetchAPI('kecamatan')" required
            class="select select-bordered select-info w-full max-w-md">
            <option v-for="kab in dataWilayah.kabKota" :key="kab.id" :value="kab.id">{{ kab.name }}</option>
          </select>
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Kecamatan</span>
          </label>
          <select v-model="formData.kecamatan" @change="fetchAPI('kelurahan')" required
            class="select select-bordered select-info w-full max-w-md">
            <option v-for="kec in dataWilayah.kecamatan" :key="kec.id" :value="kec.id">{{ kec.name }}</option>
          </select>
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Kelurahan/Desa</span>
          </label>
          <select v-model="formData.kelurahan" required class="select select-bordered select-info w-full max-w-md">
            <option v-for="kel in dataWilayah.kelurahan" :key="kel.id" :value="kel.id">{{ kel.name }}</option>
          </select>
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Alamat</span>
          </label>
          <input v-model="formData.alamat" type="text" maxlength="255" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">RT</span>
          </label>
          <input v-model="formData.rt" type="text" required class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">RW</span>
          </label>
          <input v-model="formData.rw" type="text" required class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Penghasilan Sebelum Pandemi</span>
          </label>
          <input v-model.number="formData.penghasilanSebelum" type="number" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Penghasilan Setelah Pandemi</span>
          </label>
          <input v-model.number="formData.penghasilanSetelah" type="number" required
            class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="label">
            <span class="label-text">Alasan Membutuhkan Bantuan</span>
          </label>
          <select v-model="formData.alasan" required class="select select-bordered select-info w-full max-w-md">
            <option value="">Pilih</option>
            <option value="Kehilangan pekerjaan">Kehilangan pekerjaan</option>
            <option value="Kepala keluarga">Kepala keluarga</option>
            <option value="Tergolong fakir/miskin">Tergolong fakir/miskin</option>
            <option value="Lainnya">Lainnya</option>
          </select>
        </div>
        <div class="form-control" v-if="formData.alasan === 'Lainnya'">
          <label class="label">
            <span class="label-text">Alasan Lainnya</span>
          </label>
          <input v-model="formData.alasanLainnya" type="text" class="input input-bordered input-info w-full max-w-md" />
        </div>
        <div class="form-control">
          <label class="cursor-pointer label">
            <input type="checkbox" v-model="formData.konfirmasi" required class="checkbox checkbox-warning mr-3" />
            <span>Saya menyatakan bahwa data yang diisikan adalah benar dan siap
              mempertanggungjawabkan apabila ditemukan ketidaksesuaian dalam
              data tersebut.</span>
          </label>
        </div>
        <button type="submit" class="btn btn-warning mt-4">Submit</button>
      </form>
    </div>
    <div class="basis-1/2 mt-14 lg:mt-0">
      <h1 class="text-2xl font-bold my-4">Preview Data Penerima Bansos</h1>
      <div class="mockup-code">
        <div v-if="showPreview" class="mt-8">
          <pre class="bg-base-200 p-4 rounded">{{
            JSON.stringify(formData, null, 2)
          }}</pre>
        </div>
        <div class="mt-8" v-else>
          <pre class="bg-base-200 p-4 rounded">
No Data to Preview, please fill the form first</pre>
        </div>
      </div>
    </div>
  </div>

  <div class="toast toast-top toast-end">
    <div class="alert alert-success" v-if="isSuccess == true">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 shrink-0 stroke-current" fill="none" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
          d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <span>Data berhasil dikirim</span>
    </div>
    <div class="alert alert-error" v-else-if="isSuccess == false">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 shrink-0 stroke-current" fill="none" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
          d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <span>Interval Server Error: Traffic Overloaded!</span>
    </div>
    <div v-else></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        nama: "",
        nik: "",
        nomorKartuKeluarga: "",
        fotoKTP: null,
        fotoKartuKeluarga: null,
        umur: null,
        jenisKelamin: "",
        provinsi: "",
        kabKota: "",
        kecamatan: "",
        kelurahan: "",
        alamat: "",
        rt: "",
        rw: "",
        penghasilanSebelum: "",
        penghasilanSetelah: "",
        alasan: "",
        alasanLainnya: "",
        konfirmasi: false,
      },
      dataWilayah: {
        provinsi: [],
        kabKota: [],
        kecamatan: [],
        kelurahan: [],
      },
      showPreview: false,
      dataWilayahID: {
        provinsi: null,
        kabKota: null,
        kecamatan: null,
        kelurahan: null,
      },
      showPreview: false,
      isSuccess: undefined,
    };
  },
  methods: {
    async fetchAPI(scope) {
      const API = "https://www.emsifa.com/api-wilayah-indonesia/api";
      let link =
        scope == "kabKota"
          ? API + `/regencies/${this.formData.provinsi}.json`
          : scope == "kecamatan"
            ? API + `/districts/${this.formData.kabKota}.json`
            : scope == "kelurahan"
              ? API + `/villages/${this.formData.kecamatan}.json`
              : API + "/provinces.json";

      const response = await fetch(link);
      this.dataWilayah[scope] = await response.json();
    },
    handleFileUpload(field, event) {
      this.formData[field] = event.target.files[0];
    },
    async handleSubmit() {
      this.showPreview = true;
      this.isSuccess = Math.random() > 0.5; // Simulate success/failure
      console.log(this.isSuccess);

      setTimeout(() => {
        $(".alert")
          .fadeTo(500, 0)
          .slideUp(500);
      }, 1500); // Simulate response time
    },
  },
  mounted() {
    this.fetchAPI('provinsi');
  },
};
</script>
