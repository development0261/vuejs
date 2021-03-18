<template>
    <div class="session-wrapper">
        <div class="session-right session-center text-center">
            <div class="session-table-cell">
                <div class="session-content">
                    <h2 class="mb-4">{{$t('message.signUp')}}</h2>
                    <p class="fs-14">
                        {{$t('message.havingAnAccount')}}
                    </p>
                    <v-form v-model="valid" class="mb-5">
                        <div style="display: none"><v-text-field label="HopLink" v-model="hoplink" :value="hoplink" disabled="true"></v-text-field></div>
                        <v-text-field label="First Name"
                                      v-model="fname"
                                      :rules="fnameRules"
                                      required></v-text-field>
                        <v-text-field label="Last Name"
                                      v-model="lname"
                                      :rules="lnameRules"
                                      required></v-text-field>
                        <v-text-field label="E-mail ID"
                                      v-model="email"
                                      :rules="emailRules"
                                      required></v-text-field>
                        <v-btn large @click="submit" block color="primary" class="mb-3">{{$t('message.signUp')}}	</v-btn>
                        <v-btn large @click="onSigninMember" block color="warning" class="mb-2">{{$t('message.loginNow')}}</v-btn>
                    </v-form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import SessionSliderWidget from "Components/Widgets/SessionSlider";
    import AppConfig from "Constants/AppConfig";

    export default {
        components: {
            SessionSliderWidget
        },
        data() {
            return {
                valid: false,
                fname: "",
                hoplink: "",
                fnameRules: [
                    v => !!v || "First Name is required"
                ],
                lname: "",
                lnameRules: [
                    v => !!v || "Last Name is required"
                ],
                email: "",
                emailRules: [
                    v => !!v || "E-mail is required",
                    v =>
                        /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) ||
                        "E-mail must be valid"
                ],
                appLogo: AppConfig.appLogo2,
                brand: AppConfig.brand
            };
        },
        methods: {
            submit() {
                let userDetail = {
                    fname: this.fname,
                    lname: this.lname,
                    email: this.email,
                    password: "default-password",
                    hoplink: this.hoplink
                };
                this.$store.dispatch("memberSignUp", {
                    userDetail
                });
            },
            onSigninMember() {
                this.$router.push("/member/signin");
            },
            getQueryStringParam(){
                this.hoplink = this.$route.query.hoplink;
            }
        },
        mounted(){
            this.getQueryStringParam();
        }
    };
</script>
