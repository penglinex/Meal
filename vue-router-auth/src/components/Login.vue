<template>
<div>
    <h4>Login</h4>
    <form>
        <label for="email">E-Mail Address</label>
        <div>
            <input id="email" type="email" v-model="email" required autofocus>
        </div>
        <div>
            <label for="password">Password</label>
            <div>
                <input id="password" type="password" v-model="password" required>
            </div>
        </div>
        <div>
            <button type="submit" @click="handleSubmit">
                Login
            </button>
        </div>
    </form>
</div>
</template>

<script>
export default {
    data() {
        return {
            email: "",
            password: ""
        }
    },
    methods: {
        handleSubmit(e) {
            e.preventDefault()
            if (this.password.length > 0) {
                this.$http.post('http://localhost:9000/login', {
                        email: this.email,
                        password: this.password
                    })
                    .then(response => {
                        let is_admin = response.data.user.is_admin
                        localStorage.setItem('user', JSON.stringify(response.data.user))
                        localStorage.setItem('jwt', response.data.token)

                        if (localStorage.getItem('jwt') != null) {
                            this.$emit('loggedIn')
                            if (this.$route.params.nextUrl != null) {
                                this.$router.push(this.$route.params.nextUrl)
                            } else {
                                if (is_admin == 1) {
                                    this.$router.push('admin')
                                } else {
                                    this.$router.push('dashboard')
                                }
                            }
                        }
                    })
                    .catch(function (error) {
                        console.error(error.response);
                    });
            }
        }
    }
}
</script>
