<template>
    <div id="view-employee">
        <ul class="collection with-header">
            <li class="collection-header">
                <h4>{{ name }}</h4>
            </li>
            <li class="collection-item">ID: {{employee_id}}</li>
            <li class="collection-item">Dept: {{dept}}</li>
            <li class="collection-item">Pos: {{position}}</li>
        </ul>
        <router-link to="/" class="btn gray">Back</router-link>
        <button class="btn red" @click="deleteEmployee">Delete</button>

        <div class="fixed-action-btn">
            <router-link :to="{name: 'edit-employee', params: {employee_id: this.employee_id}}" class="btn-floating btn-large red">
                <i class="fa fa-pencil"></i>
            </router-link>
        </div>
    </div>
</template>

<script>
import db from './firebaseInit'
export default {
    name: 'view-employee',
    data() {
        return {
            employee_id: null,
            name: null,
            dept: null,
            position: null
        }
    },
    beforeRouteEnter(to, from, next) {
        db.collection('employees').where('employee_id', '==', to.params.employee_id).get()
        .then(querySnapshot => {
            querySnapshot.forEach(doc => {
                next(vm => {
                    vm.employee_id = doc.data().employee_id
                    vm.name = doc.data().name
                    vm.dept = doc.data().dept
                    vm.position = doc.data().position
                })
            })
        })
    },
    watch: {
        $route: 'fetchData'
    },
    methods: {
        fetchData() {
            db.collection('employees').where('employee_id', '==', this.$route.params.employee_id).get()
            .then(querySnapshot => {
                querySnapshot.forEach(doc => {
                    this.employee_id = doc.data().employee_id
                    this.name = doc.data().name
                    this.dept = doc.data().dept
                    this.position = doc.data().position
                })
            })
        },
        deleteEmployee() {
            if(confirm('are you sure?')) {
                db.collection('employees').where('employee_id', '==', this.$route.params.employee_id).get()
                .then(querySnapshot => {
                    querySnapshot.forEach(doc => {
                        doc.ref.delete()
                        this.$router.push('/')
                    })
                })
            }
        }
    }
}
</script>

