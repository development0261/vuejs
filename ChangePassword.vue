<template>
	<div class="app-default-layout">
		<template>
			<!-- App Header -->
			<app-header></app-header>
			<!-- App Main Content -->
			<v-content>
				<div class="pt-10 pl-10 pr-10">
					<v-row no-gutters>
			      <v-col
			        cols="12"
			        sm="6"
			      >
			      <template>
						<div class="session-wrapper">
							<div class="text-center">
								<div class="session-table-cell">
									<div class="session-content">
										<h2 class="mb-4">Change Password</h2>
										<p class="fs-14">Please Enter Your Password To Change.</p>
										<v-form v-model="valid" class="5">
											<v-text-field 
												label="New Password" 
												v-model="newPassword" 
												type="password" 
												:rules="passwordRules" 
												required
											></v-text-field>
											<v-text-field 
												label="Confirm Password" 
												v-model="confirmPassword" 
												type="password" 
												:rules="passwordRules" 
												required
											></v-text-field>
											<v-btn large @click="submit" block color="primary">Change Password</v-btn>
										</v-form>
									</div>
								</div>
							</div>
						</div>
					</template>
			      </v-col>
			    </v-row>
				</div>
			</v-content>
		</template>
	</div>
</template>
<script>
import SessionSliderWidget from "Components/Widgets/SessionSlider";
import AppConfig from "Constants/AppConfig";
import webServices from 'WebServices';
import Vue from 'vue';
import router from '../../router';
import { mapGetters } from "vuex";
import Header from "Components/AdminHeader/Header.vue";

export default {
  components: {
    SessionSliderWidget
  },
  data() {
    return {
      valid: false,
      newPassword: "",
      confirmPassword: '',
		passwordRules: [v => !!v || "Password is required"],
      appLogo: AppConfig.appLogo2
    };
  },
  components: {
    appHeader: Header,
  },
  computed: {
    ...mapGetters(["selectedRouterAnimation"])
  },
  methods: {
    submit() {
    	const user = {
    		password : this.newPassword,
    		password_confirmation : this.confirmPassword,
    		email: this.admindata.email
    	}
      webServices.post('/ChangePassword ',user).
        then(response =>
        {
        	if(response.data.success == false)
        	{
		        Vue.notify({
		            group: 'loggedIn',
		            type: 'error',
		            text: response.data.validation.password[0]
		        });
		    }
		    if(response.data.success == true)
        	{
        		var local = localStorage.setItem('admin','')
				router.push("/admin/login");
        	}
        	console.log(response.data)
        }).
        catch(error => 
        {
        })
    }
  },
  mounted() {
  	var local = localStorage.getItem('admin')
    this.admindata = JSON.parse(local);
    /*console.log(this.admindata)*/
    /*if(this.admindata == null)
    {
    	router.push("/admin/login");
    }*/
  }
};
</script>
<style scoped>
.app-default-layout {
  height: 100vh;
}
</style>