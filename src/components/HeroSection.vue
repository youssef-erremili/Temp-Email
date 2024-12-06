<template>
    <div>
        <div v-if="isLoading" class="h-1 w-full fixed top-0 z-20">
            <ProgressBar mode="indeterminate" style="height:4px; background:#0f172a;"></ProgressBar>
        </div>
        <Navbar />
        <div class="text-center h-auto bg-slate-900 pt-10 pb-16 md:px-8">
            <div class="space-y-4 flex-1">
                <h1 class="text-white capitalize font-bold text-4xl xl:text-5xl">
                    free temporary email generator
                    <span class="text-indigo-600">TempMail</span>
                </h1>
                <p class="text-slate-400 text-balance text-sm text-center leading-relaxed sm:mx-auto">
                    Forget about spam, advertising mailings, hacking and attacking robots. Keep your real mailbox clean
                    and secure. Temp Mail provides temporary, secure, anonymous, free, disposable email address.
                </p>
                <div>
                    <form @submit.prevent="generateFakeDomains()">
                        <div class="py-10 w-fit m-auto rounded-md border-2 px-20 mt-10 border-slate-600 border-dashed">
                            <h2 class="text-lg font-medium text-slate-100 my-4">Your Temporary Email Address :</h2>
                            <div class="flex justify-center items-center">
                                <input v-model="tempMail" type="text" placeholder="tempMail address"
                                    class="pointer-events-none select-none bg-slate-800 rounded-full py-4 px-7 w-[30rem] text-lg text-white font-sans border-none outline-none"
                                    name="tempmail" readonly>
                                <button @click="copyTempMail(tempMail)" type="button"
                                    class="cursor-pointer rounded-full h-auto bg-indigo-600 ml-2 p-4">
                                    <IconCopy color="#fff" />
                                </button>
                            </div>
                        </div>
                    </form>
                    <div class="flex justify-evenly w-[50rem] mx-auto mt-12">
                        <button :disabled="isLoading"
                            class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px] px-10 bg-indigo-600 text-white"
                            @click="generateFakeDomains()">
                            <IconGenerate class="w-[22.5px] mt-[2px] mr-2" />
                            generate
                        </button>
                        <button :disabled="isLoading"
                            class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px] px-10 bg-indigo-600 text-white"
                            @click="copyTempMail(tempMail)">
                            <IconCopy class="w-[22.5px] mr-2" />
                            copy
                        </button>
                        <button :disabled="isLoading"
                            class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px]  px-10 bg-indigo-600 text-white"
                            @click="refreshPage()">
                            <IocnRefresh class="w-[22.5px] mr-2" />
                            refresh
                        </button>
                        <button :disabled="isLoading"
                            class="flex items-center capitalize text-[17px] font-[400] rounded-3xl h-[43px]  px-10 bg-indigo-600 text-white">
                            <IconDelete class="w-[22.5px] mr-2" />
                            delete
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <inBox :isWait="isWait">
            <RecievedMsg v-if="isMsg">
                <div v-for="(msg, index) in messages" :key="index" :class="index === 0 ? '' : 'mt-3'"
                    class="flex items-center justify-between bg-white py-2 px-4">
                    <div class="w-1/3">
                        <h1 class="font-medium text-slate-900 uppercase">{{ msg.fullname }}</h1>
                        <p class="text-sm text-slate-500">{{ msg.address }}</p>
                    </div>
                    <div class="w-1/3 text-center pl-[1.3rem] capitalize">
                        <h3>{{ msg.subject }}</h3>
                    </div>
                    <div class="w-1/3 text-right">
                        <button type="button" @click="getMessage(msg.id)"
                            class="bg-indigo-500 rounded-md text-base capitalize text-white py-1 px-4">view</button>
                    </div>
                </div>
            </RecievedMsg>
        </inBox>
    </div>
    <div class="flex justify-center">
        <Dialog v-model:visible="visible" maximizable modal header="Inbox" :style="{ width: '65rem' }"
            :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
            <div class="m-0 text-slate-400 flex justify-between px-3 py-4 text-[15px] border-b-2 border-slate-700">
                <section>
                    <p class="mt-2">Full Name: {{ message.sender }}</p>
                    <p>Email Address: {{ message.address }}</p>
                </section>
                <section>
                    <p class="mt-2">Subject: {{ message.subject }}</p>
                    <p>BCC: {{ message.bcc }}</p>
                </section>
                <section>
                    <p>CC: {{ message.cc }}</p>
                </section>
            </div>
            <div v-html="message.html" class="text-slate-400 py-4 my-5 px-3 text-center w-[40rem] mx-auto"></div>
        </Dialog>
    </div>
</template>


<script>
import Navbar from './Navbar.vue';
import IconCopy from './icons/IconCopy.vue';
import { useToast } from 'vue-toast-notification';
import ProgressBar from 'primevue/progressbar';
import IocnRefresh from './icons/IocnRefresh.vue';
import IconDelete from './icons/IconDelete.vue';
import IconGenerate from './icons/IconGenerate.vue';
import inBox from './inBox.vue';
import RecievedMsg from './RecievedMsg.vue';
import Dialog from 'primevue/dialog';




export default {
    components: {
        Navbar,
        IconCopy,
        ProgressBar,
        IocnRefresh,
        IconDelete,
        IconGenerate,
        inBox,
        RecievedMsg,
        Dialog
    },
    data() {
        return {
            tempMail: "",
            token: "",
            passWord: "youssef2000!@ER",
            isLoading: false,
            visible: false,
            isMsg: false,
            isWait: true,
            messages: [],
            message: {},
            intervalId: null
        }
    },
    beforeDestroy() {
        if (this.intervalId) {
            clearInterval(this.intervalId);
        }
    },
    methods: {
        toastNotification(message, type) {
            const $toast = useToast();
            $toast.open({
                message: message,
                type: type,
                duration: 1800,
                dismissible: true,
                position: 'top-right'
            });
        },

        generateFakeDomains() {
            this.tempMail = "Loading..."
            this.isLoading = true

            this.messages = [];
            this.message = {};
            this.isWait = true;
            this.isMsg = false;

            const Numbers = "0123456789"
            let GeneratedUser = "";
            let FullUser = ""
            for (let i = 0; i < 3; i++) {
                GeneratedUser += Numbers.charAt(Math.floor(Math.random() * Numbers.length))
            }
            fetch("https://randomuser.me/api/")
                .then(response => {
                    if (response.ok) {
                        return response.json();
                    } else {
                        throw new Error('Network response was not ok');
                    }
                })
                .then(data => {
                    let FirstName = data.results[0].name.first
                    let LastName = data.results[0].name.last
                    FullUser = FirstName + LastName + GeneratedUser
                    return FullUser.toLowerCase();
                })
                .then(FullUser => {
                    this.messages = [];
                    this.message = {};
                    this.isWait = true;
                    this.isMsg = false;
                    return this.tempMailEngine(FullUser)
                })
                .catch(error => {
                    console.error('There has been a problem with your fetch operation:', error);
                    this.toastNotification('Failed to generate domain name. Please try again.', 'error');
                    this.tempMail = "error domain name";
                    this.isLoading = false;
                });
        },

        tempMailEngine(userName) {
            fetch('https://api.mail.tm/domains')
                .then(response => {
                    if (response.ok) {
                        return response.json();
                    } else {
                        this.toastNotification('Network response was not ok', 'warning')
                        throw new Error('Network response was not ok');
                    }
                })
                .then(data => {
                    const domain = data['hydra:member'][0].domain;
                    this.tempMail = `${userName}@${domain}`;
                    this.isLoading = false;
                })
                .then(() => {
                    this.messages = [];
                    this.message = {};
                    this.isWait = true;
                    this.isMsg = false;
                    return this.createAccount();
                })
                .catch(error => {
                    this.isLoading = false;
                    this.tempMail = "try again";
                    console.error('There was a problem with the fetch operation:', error);
                    this.toastNotification('Failed to process temporary email. Please try again.', 'error');
                });
        },

        createAccount() {
            fetch('https://api.mail.tm/accounts', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    address: this.tempMail,
                    password: this.passWord
                })
            })
                .then(response => {
                    if (response.ok) {
                        return response.json();
                    }
                })
                .then((data) => {
                    if (data) {
                        this.toastNotification('TempMail created successfully', 'success')
                    }
                    else {
                        this.tempMail = "error to create account";
                        this.toastNotification('Please refresh the page for another account', 'error');
                    }
                })
                .then(() => {
                    return this.getToken();
                })
                .catch(error => {
                    this.tempMail = "error"
                    this.toastNotification('Failed to create account. Please try again.', 'error');
                    console.error('There was a problem with the fetch operation:', error);
                });
        },

        getToken() {
            fetch('https://api.mail.tm/token', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    address: this.tempMail,
                    password: this.passWord
                })
            })
                .then(response => {
                    if (response) {
                        return response.json();
                    }
                })
                .then(data => {
                    this.token = data.token
                })
                .then(() => {
                    setTimeout(() => {
                        this.intervalId = setInterval(() => {
                            this.fetchMessages();
                        }, 3000);
                    }, 6000);
                })
                .catch(error => {
                    this.tempMail = "error";
                    this.toastNotification('Failed to retrieve token. Please try again.', 'error');
                    console.error('There was a problem with the fetch operation:', error);
                });
        },

        fetchMessages() {
            fetch('https://api.mail.tm/messages', {
                method: 'GET',
                headers: {
                    'Authorization': `Bearer ${this.token}`
                }
            })
                .then(response => {
                    if (response.ok) {
                        return response.json();
                    } else {
                        throw new Error('Network response was not ok');
                    }
                })
                .then(data => {
                    if (!data['hydra:member'] || data['hydra:member'].length === 0) {
                        this.isMsg = false
                        this.isWait = true
                    } else {
                        this.isMsg = true
                        this.isWait = false
                        data['hydra:member'].forEach(msg => {
                            const newMessage = {
                                fullname: msg["from"]["name"],
                                address: msg["from"]["address"],
                                subject: msg["subject"],
                                id: msg["id"],
                            };

                            const exists = this.messages.some(existingMsg => existingMsg.id === newMessage.id);
                            if (!exists) {
                                this.messages.push(newMessage);
                            }
                        });
                    }
                })
                .catch(error => {
                    this.isMsg = false;
                    this.isWait = true;
                    this.toastNotification('Failed to fetch messages. Please try again.', 'error');
                    console.error('There was a problem with the fetch operation:', error);
                    if (this.intervalId) {
                        clearInterval(this.intervalId);
                    }
                });
        },

        getMessage(messageId) {
            fetch(`https://api.mail.tm/messages/${messageId}`, {
                method: 'GET',
                headers: {
                    'Authorization': `Bearer ${this.token}`,
                    'Content-Type': 'application/json'
                }
            })
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    if (data) {
                        this.message = {
                            sender: data.from['name'],
                            address: data.from['address'],
                            subject: data.subject,
                            content: data.intro,
                            bcc: data.bcc.length > 0 ? data.bcc[0].address : "No Bcc available",
                            cc: data.cc.length > 0 ? data.cc[0].address : "No Cc available",
                            html: data.html
                        };
                        this.visible = true
                    }
                })
                .catch(error => {
                    console.error('Error fetching message:', error);
                    this.toastNotification('Failed to retrieve message. Please try again.', 'error');
                });
        },

        refreshPage() {
            console.clear()
            this.$router.go();
        },

        copyTempMail(text) {
            if (this.tempMail !== "") {
                navigator.clipboard.writeText(text)
                    .then(() => {
                        this.toastNotification('tempMail copied successfully', 'info')
                    })
                    .catch(err => {
                        this.toastNotification('Could not copy text!', 'error');
                        console.log(err)
                    });
            }
        },
    },

    mounted() {
        this.messages = [];
        this.message = {};
        this.isWait = true;
        this.isMsg = false;

        return this.generateFakeDomains();
    }
}
</script>