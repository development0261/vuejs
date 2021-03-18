<template>
	<div class="app-default-layout">
		<template>
			<!-- App Header -->
			<app-header></app-header>
			<!-- App Main Content -->
			<v-content>
				<div class="pt-10 pl-10 pr-10">
					<div>
						<h5>Members</h5>
					</div>
					<v-data-table
						v-bind:headers="headers"
						v-bind:items="items"
						v-bind:search="search"
					>
						<template slot="items" slot-scope="props">
							<td>
								<v-edit-dialog lazy>
									{{ props.item.name }}
									<v-text-field
										slot="input"
										label="Edit"
										v-model="props.item.name"
										single-line
										counter
										:rules="[max25chars]">
									</v-text-field>
								</v-edit-dialog>
							</td>
							<td>{{ props.item.calories }}</td>
							<td>{{ props.item.fat }}</td>
							<td>{{ props.item.carbs }}</td>
							<td>{{ props.item.protein }}</td>
							<td>{{ props.item.sodium }}</td>
							<td>{{ props.item.calcium }}</td>
							<td>
								<v-edit-dialog
									@open="tmp = props.item.iron"
									@save="props.item.iron = tmp || props.item.iron"
									large
									lazy
									>
								<div>{{ props.item.iron }}</div>
								<div slot="input" class="mt-3 title">Update Iron</div>
								<v-text-field
									slot="input"
									label="Edit"
									v-model="tmp"
									single-line
									counter
									autofocus
									:rules="[max25chars]"
									>
								</v-text-field>
								</v-edit-dialog>
							</td>
						</template>
						<template slot="pageText" slot-scope="{ pageStart, pageStop }">
							From {{ pageStart }} to {{ pageStop }}
						</template>
					</v-data-table>
				</div>
			</v-content>
		</template>
	</div>
</template>

<script>
import api from "../../api";
import webServices from 'WebServices';
import { mapGetters } from "vuex";
import Header from "Components/AdminHeader/Header.vue";

export default {
  data() {
    return {
      loader: true,
      max25chars: v => v.length <= 25 || "Input too long!",
      tmp: "",
      search: "",
      pagination: {},
      headers: [
        { text: "First Name", value: "fname" },
        { text: "Last Name", value: "lname" },
        { text: "Email", value: "email" },
        { text: "User type", value: "user_type" },
      ],
      items: []
    };
  },
  components: {
    appHeader: Header,
  },
  computed: {
    ...mapGetters(["selectedRouterAnimation"])
  },
  mounted() {
    this.getTablesData();
  },
  methods: {
    getTablesData() {
      const user_type = {
                    user_type: 'member'
                };

                webServices.post('/GetUsers ',user_type).
                then(response =>
                {
                	this.items = response.data.members;
                }).
                catch(error => 
                {
                })
    }
  }
};
</script>

<style scoped>
.app-default-layout {
  height: 100vh;
}
</style>

