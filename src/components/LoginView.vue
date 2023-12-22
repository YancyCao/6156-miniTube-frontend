<template>
    <el-form :model="form" label-width="300px">
        <el-form-item label="I have an account!">
            <el-switch v-model="form.login" />
        </el-form-item>
        <el-form-item label="Email Address">
            <el-input v-model="form.email" />
        </el-form-item>
        <el-form-item label="Verification Code" class="verification_code">
            <el-input v-model="form.code" class="hi" />
            <el-button @click="onSendCode">Send Verification Code</el-button>
        </el-form-item>
        <el-form-item>
            <el-button type="primary" @click="onSubmit"> {{ form.login ? "Login" : "Create" }} </el-button>
        </el-form-item>
    </el-form>
</template>
  
<script lang="js" setup>
import { ElMessage } from 'element-plus'
import { inject, reactive } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const router = useRouter()

const globalState = inject('globalState')
// do not use same name with ref
const form = reactive({
    login: false,
    email: '',
    code: '',
})

const onSubmit = async () => {
    //localStorage.clear();
    if (form.login) {
        const email = form.email
        const test = (await axios.post(`${globalState.origin.value}/verify-login/`, { email: email, code: form.code })).data
        if (test.status === 'login successful') {
            globalState.username.value = email
            router.push({
                name: 'PersonalPage',
                params: {UserName:globalState.username.value},
            })
        }
        ElMessage({
            message: test.status,
            type: test.status === 'login successful' ? 'success' : 'error',
        })
    } else {
        const test = (await axios.post(`${globalState.origin.value}/verify/`, { email: form.email, code: form.code })).data
        ElMessage({
            message: test.status,
            type: test.status === 'verified' ? 'success' : 'error',
        })
    }
}
const onSendCode = async () => {
    if (form.login) {
        const test = (await axios.post(`${globalState.origin.value}/login/`, { email: form.email })).data
        ElMessage({
            message: test.status,
            type: test.status === 'verification email sent' ? 'success' : 'error',
        })
    } else {
        console.log(`${globalState.origin.value}/register/`, { email: form.email })
        const test = (await axios.post(`${globalState.origin.value}/register/`, { email: form.email })).data
        ElMessage({
            message: test.status,
            type: test.status === 'subscribtion email sent' || test.status === 'verification code sent' ? 'success' : 'error',
        })
    }
}
</script>

<style scoped> .verification_code {
     display: flex;
     flex-direction: row;
     justify-content: space-between;
 }

 .hi {
     flex: 1;
     padding-right: 10px;
 }
</style>
