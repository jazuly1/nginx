<template>
    <form @submit.prevent="add" enctype="multipart/form-data">
        <div class="form-group">
            <label for="releasedate">Artist</label>
            <div class="jy_msc_form_row row input-group input-file">
                <multi-select class="jy_msc_multiselec col-lg-11 col-md-11 col-sm-11 col-xs-8" v-model="single.artisat" :options="artist"
                        :selected-options="items"
                        placeholder="select item"
                        @select="onSelect">
                </multi-select>
                <span class="jy_msc_reset_but input-group-btn col-lg-1 col-md-11 col-sm-1 col-xs-4">
                    <button class="btn btn-danger btn-reset jy_msc_btn_reset" type="button" @click="reset">Reset</button>
                </span>
            </div>
        </div>
        <div class="form-group">
            <label for="singlename">Single Name</label>
            <input type="text" class="form-control" v-model="single.singlename" placeholder="Single Name">
        </div>
        <div class="form-group">
            <label for="releasedate">Release Date</label>
            <datetime class="form-control" v-model="single.date" placeholder="Release Date"></datetime>
        </div>
        <div class="form-group">
            <div class="large-12 medium-12 small-12 cell">
                <label for="files">Sample Song
                    <input type="file" id="files" ref="files" class="jy_msc_sample_song_files" multiple v-on:change="handleFilesUpload()"/>
                </label>
            </div>
            <div class="large-12 medium-12 small-12 cell">
                <div v-for="(file, key) in files" class="file-listing">{{ file.name }} <span class="remove-file" v-on:click="removeFile( key )">Remove</span></div>
            </div>
                <br>
            <div class="large-12 medium-12 small-12 cell">
                <button v-on:click="addFiles()">Add Files</button>
            </div>
        </div>
        <div class="form-group">
            <div class="form-group">
                <label for="datebirth">Cover/Image</label>
                <input type="file" class="form-control-file" id="files" name="files" v-on:change="onImageChange"  />
            </div>
        </div>
        <input type="submit" value="Save" class="btn btn-primary">
    </form>
</template>

<script>
    export default {
        data() {
            return {
                files: [],
                errors: [],
                artistname: [],
                single: {},
                image: '',
                success: '',
                artist: [],
                showinput: [],
                searchText: '',
                items: [],
                lastSelectItem: {}
            }
        },
        methods: {
            getData() {
                axios.get('/api/artist/getartist')
                    .then((response) => {
                        this.artist = response.data.artist;
                        this.single.artistinsingle = response.data.artist
                        console.log(this.single.artistinsingle);
                    });
                
            },
            onSelect (items, lastSelectItem) {
                this.items = items
                this.lastSelectItem = lastSelectItem
                //this.single.artistinsingle = items
                this.single.artist = items
            },
            reset () {
                this.items = []
            },
            addFiles(){
                this.$refs.files.click();
            },
            handleFilesUpload(){
                let uploadedFiles = this.$refs.files.files;
                for( var i = 0; i < uploadedFiles.length; i++ ){
                    this.files.push( uploadedFiles[i] );
                }
            },
            removeFile( key ){
                this.files.splice( key, 1 );
            },
            onImageChange(e) {
                this.image = e.target.files[0];
            },
            backEndDateFormat: function(date) {
                var date = new Date(date);
                return moment(date, 'DD/MM/YYYY').format('YYYY-MM-DD');
            },
            add(e) {
                e.preventDefault();
                let currentObj = this;
 
                const config = {
                    headers: { 'content-type': 'multipart/form-data' }
                }
 
                let formData = new FormData();

                if (this.single.date){
                    this.single.date = this.backEndDateFormat(this.single.date)
                }

                for( var i = 0; i < this.files.length; i++ ){
                    let file = this.files[i];

                    formData.append('file[]', file );
                }
                
                formData.append('image', this.image);
                //formData.append('singlename', this.$data.single.singlename);
                //formData.append('date', this.$data.single.date);
                //formData.append('artist[]', this.artist);

                axios.post('/select-files', formData, this.$data.single, config)
                    .then((response) => {
                        alert('Data Single Successfull Inserted.')
                        //this.$router.push('/single/');
                        //console.log(this.$data.single.artist);
                        //console.log(this.image);
                        //console.log(this.$data.single.singlename);
                        //console.log(this.$data.single.date);
                        console.log(response);
                        //this.showinput = response.data.input;
                    })
                    .catch(function (error) {
                        alert('There is an error when trying to insert data artist.')
                    });
            }
        },
            mounted() {
                console.log('Add Single Mounted.')
                this.getData()
            }
    }
</script>

<style>
    input.vdatetime-input {
        width: 100%;
        height: 100%;
        border: none;
    }
    .jy-msc-file-selector {
        border: 1px solid #d2d2d2;
    }
    .multiple.search .delete.icon:before {
        font-family: sans-serif;
        content: "x";
        font-style: normal; 
    }
    .jy_msc_form_row {
        margin: auto;
        border: 1px solid #ced4da;
    }
    .jy_msc_reset_but {
        padding: 0;
    }
    .jy_msc_btn_reset {
        width: 100%;
        height: 100%;
    }
    input.jy_msc_sample_song_files[type="file"]{
        position: absolute;
        top: -500px;
    }
    div.file-listing{
        width: 200px;
    }
    span.remove-file{
        color: red;
        cursor: pointer;
        float: right;
    }
</style>
