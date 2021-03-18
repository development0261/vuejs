<template>
   <v-list-item class="sidebar-profile">
		<v-list-item-avatar>
			<img src="/static/avatars/user-13.jpg" alt="avatar" height="40" width="40" class="img-responsive" />
		</v-list-item-avatar>
		<v-list-item-content class="ml-3">
			<v-list-item-title><span>Admin</span></v-list-item-title>
		</v-list-item-content>
		<v-menu 
			bottom
			offset-y
			left
			content-class="userblock-dropdown" 
			nudge-top="-10"
			nudge-right="0"
			transition="slide-y-transition"
		>	
			<template v-slot:activator="{ on }">
				<v-btn dark icon v-on="on" class="ma-0">
					<v-icon>more_vert</v-icon>
				</v-btn>
			</template>
			<div class="dropdown-content">
				<div class="dropdown-top white--text primary">
					<span class="white--text fs-14 fw-bold d-block">Admin</span>
				</div>
				<v-list class="dropdown-list">
					<template v-for="userLink in userLinks">
						<template v-if="userLink.id !== 4">						
							<v-list-item :to="getMenuLink(userLink.path)" :key="userLink.id">
								<i :class="userLink.icon"></i>
								<span>{{$t(userLink.title)}}</span>
							</v-list-item>
						</template>
						<template v-else>
							<v-list-item @click="logoutUser" :key="userLink.id">
								<i :class="userLink.icon"></i>
								<span>{{$t(userLink.title)}}</span>
							</v-list-item>
						</template>
					</template>	
				</v-list>
			</div>
		</v-menu>
   </v-list-item>
</template>

<script>
import { getCurrentAppLayout } from "Helpers/helpers";
import router from '../../router';

export default {
	data() {
		return {
			userLinks: [
				{
					id: 1,
					title: 'message.userProfile',
					icon: 'ti-user mr-3 primary--text',
					path: '/ChangePassword'
				},
				{
					id: 4,
					title: 'message.logOut',
					icon: 'ti-power-off mr-3 error--text'
				}
			]
		}
	},
	methods: {
		logoutUser() {
			var local = localStorage.setItem('admin','')
			router.push("/admin/login");
		},
		getMenuLink(path) {
			return '/' + getCurrentAppLayout(this.$router) +  path;
		}
	}
};
</script>
