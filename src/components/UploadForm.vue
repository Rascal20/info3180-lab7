<template>
    
    <div class=form> 
        <h1> Upload Form </h1>
        <div v-if="errors.length" class="alert alert-danger" role="alert">
            <ul>
                <li v-for="error in errors" :key="errors.indexOf(error)">{{ error }}</li>
            </ul>
        </div>

        <div v-if="success" class="alert alert-success" role="alert">
        <p> File Upload Successful </p>
        </div>
        

        <form @submit.prevent="uploadPhoto" method="POST" enctype="multipart/form-data" id="uploadForm">
            <div class="form-group">
                <label for="description"> Description </label><br>
                <textarea name="description" class="form-control"></textarea><br>
                
                <label for="photo"> Photo Upload </label><br>
                <input type="file" name="photo" class="form-control" id="photo"><br>
            </div>
            <button type="submit" class="btn btn-primary mb-2">Submit</button>
        </form>
    </div>
</template>
    
<script>
    export default {
        name: 'UploadForm',
        data() {
            return {
                csrf_token: '', 
                errors: [],
                success: false
            };
        },
        created() {
            this.getCsrfToken();
        },
        methods: {
            uploadPhoto(){
                let uploadForm = document.getElementById('uploadForm');
                let form_data = new FormData(uploadForm);
                let self = this

                fetch("/api/upload", {
                    method: 'POST',
                    body: form_data,
                    headers: {
                        'X-CSRFToken': this.csrf_token
                    }
                })
                .then(function (response) {
                    return response.json();
                    })
                .then(function (data) {
                    // display a success message
                    console.log(data);
                    if ('errors' in data){
                        self.errors = [...data.errors]
                        self.success = false
                    } 
                    else {
                        self.errors = []
                        self.success = true
                    }
                })
                    .catch(function (error) {
                        console.log(error);
                        });
            },
            getCsrfToken() {
                let self = this;
                fetch('/api/csrf-token')
                    .then((response) => response.json())
                    .then((data) => {
                        console.log(data);
                        self.csrf_token = data.csrf_token;
                })
            }
        }
    }

</script>

<style scoped>
    .form{
    padding: 1rem 4rem;
    }

    .form-group{
        width: 50%;
    }

    .form-group #photo{
        border: none;
        width: 30%;
    }

</style>
