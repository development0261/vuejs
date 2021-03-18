Here I have  used actions for call login function and if user data is correct then I have call mutations method for login success.

1. This is employee login form. 
	ex: employee.vue

	employeeSignIn()
	{
	    const user =
	    {
		    email: this.email,
			password: this.password,
			remember_me: this.remember_me
		};
		this.$store.dispatch("employeeSignIn",
		{
			user
		});
	},

============================================

2. call vuex actions 

const actions = {
	employeeSignIn(context, payload) {
        const { user } = payload;
        context.commit('loginUser');
        webServices.post('/employee-signin', JSON.stringify(user), { headers: { 'Content-Type': 'application/json' } })
            .then(response => {
                if (response.data.success) {
                    Nprogress.done();
                    setTimeout(() => {
                        context.commit('loginUserSuccess', user);
                    }, 500);
                } else {
                    context.commit('loginUserFailure', response.data);
                }
            })
            .catch(error => {
                console.log(error);
                console.log("Failed");
            })
    },
}
============================================
3. call vuex mutations on login success

const mutations = {
	loginUserSuccess(state, user) {
        state.user = user;
        localStorage.setItem('user', user);
        state.isUserSigninWithAuth0 = false
        router.push("/default/dashboard/ecommerce");
        setTimeout(function () {
            Vue.notify({
                group: 'loggedIn',
                type: 'success',
                text: 'User Logged In Success!'
            });
        }, 1500);
    },
}

============================================
4. use actions and mutations in main.js

new Vue({
    actions,
    mutations,
    render: h => h(App),
    components: { App }
}).$mount('#app')

============================================