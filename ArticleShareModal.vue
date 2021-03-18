<template>
    <!-- EMAIL SHARE MODAL -->
    <base-modal name="articleShare">
        <template #header>
            <h3 class="modal-title">Email this to a friend</h3>
        </template>

        <template #body>
            <div class="message message--error" v-html="errorMsg" v-if="errorMsg"></div>

            <form method="post" @submit.prevent="shareEmail" class="blog-share-form" ref="shareForm">
                <slot name="csrf"></slot>
                <div class="form-group">
                    <label>Send to Email</label>
                    <div><input type="text" name="share-email" class="input" v-model="emailShareEmail" required/></div>
                </div>

                <div class="form-group">
                    <label>Your name</label>
                    <input type="text" name="sharer-name" class="input" v-model="emailSharerName" required/>
                </div>

                <div class="form-group">
                    <label>Your email</label>
                    <input type="text" name="sharer-email" class="input" v-model="emailSharerEmail" required/>
                </div>

                <!-- showing captcha only on production -->
                <div ref="recaptcha" class="share-recaptcha"></div>

                <!-- LOADING -->
                <div class="clear"></div>
            </form>

            <!-- PROCESSING REQUEST -->
            <div class="loading" v-if="sharingArticle">
                <div class="loader"></div>
                <div class="wait">Please wait... Sending message...</div>
            </div>

            <!-- RESPONSE -->
            <div class="success" v-if="sharingFinished && successMsg">
                <div class="message" v-html="successMsg"></div>
                <div class="button-wrapper">
                    <a href="javascript:void();" @click="resetEmailShare(true)" class="btn2">Send another</a>
                </div>
            </div>
        </template>

        <template #footer>
            <button type="button" class="button" @click="shareEmail" v-if="!successMsg && !sharingArticle">send</button>
            <button type="button" class="button" @click="$modal.hide('articleShare')">close</button>
        </template>
    </base-modal>
</template>
<script>
    import BaseModal from './BaseModal';

    export default {
        name: "ArticleShareModal",

        components: {
            BaseModal
        },

        props: {
            articleSlug: {
                required: true,
                type: String
            },
            url: {
                required: true
            },
            preview: {
                type: Boolean,
                required: false,
                default: true
            },
            recaptchaKey: {
                required: false
            }
        },

        data() {
            return {
                sharingArticle: false,
                sharingFinished: false,
                statsLoaded: false,
                successMsg: "",
                errorMsg: "",
                emailSharerName: "",
                emailSharerEmail: "",
                emailShareEmail: "",
                recaptchaLoaded: false
            }
        },

        mounted() {
            this.$events.$on('open-share-modal', flag => this.resetEmailShare(flag));
        },

        methods: {
            resetEmailShare(another) {
                this.sharingFinished = false;
                this.successMsg = "";
                this.errorMsg = "";
                //Fields data
                this.emailShareEmail = "";
                this.emailSharerName = "";
                this.emailSharerEmail = "";

                //Opens modal
                this.$modal.show('articleShare');

                if (another) {
                    //Resets recaptcha
                    grecaptcha.reset();
                }
                //Starts recaptcha on first opening
                else {
                    setTimeout(() => {
                        grecaptcha.render(
                            this.$refs.recaptcha,
                            {"sitekey": this.recaptchaKey}
                        );
                    }, 1000);
                    this.recaptchaLoaded = true;
                }
            },

            /**
             * Shares the article via email
             * @return void
             */
            async shareEmail() {
                this.sharingArticle = true;
                this.sharingFinished = false;
                this.successMsg = "";
                this.errorMsg = "";
                const data = new FormData(this.$refs.shareForm);
                let response;

                try {
                    response = await axios.post(this.url + '/' + this.articleSlug + '/share', data);

                    this.sharingArticle = false;
                    if (response.status > 199 && response.status < 300) {
                        this.sharingFinished = true;
                        this.successMsg = response.data.success;
                    }
                } catch (error) {
                    this.sharingArticle = false;
                    response = error.response;
                }

                if (response.status === 422) {
                    this.errorMsg = Object.values(response.data.errors)[0][0];
                }
            }

        },
        beforeDestroy() {
            this.$events.$off('open-share-modal');
        }
    }
</script>

<style scoped lang="scss">
    .modal-body .loading, .success {
        color: #64656f;
        height: 100%;
        font-size: 1em;
        padding: 20% 40px 40px;
        text-align: center;
        position: absolute;
        top: 0;
        bottom: 0;
        margin-left: -16px;
        width: 100%;
        background: rgba(255, 255, 255, .7);
    }
</style>
