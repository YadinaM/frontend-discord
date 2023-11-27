<script setup>
    import { ref, onMounted } from "vue";

    let message = ref("");
    let messages = ref(['hello', 'world']);

    let socket = null;

    onMounted(() => {
        //connecteren met de Web socket server
        //socket = new WebSocket('ws://localhost:3000/primus'); om lokaal te testen
        socket = new WebSocket('wss://backend-discord.onrender.com/primus');

        socket.onmessage = (event) => {
            let newMessage = JSON.parse(event.data);
            if(newMessage.data.action === "newMessage"){
                message.value.push(newMessage.data.message);
            } 
            //message.value.push(newMessage.message); //
        }
    })

    const sendMessage = () => {
        let newMessage = {
            "message": message.value,
            "action": "newMessage"
        }

        //send message to socket server
        socket.send(JSON.stringify(newMessage));

        //add message value to messages array
        //messages.value.push(message.value); zelf in array gestopt voor demo
        message.value = ""; //input leegmaken nadat bericht verzonden is
    }

</script>

<template>
  <div>
    <ul>
        <li v-for="m in messages">{{ m }}</li>
    </ul>

    <div>
        <input v-model = "message" type="text" />
        <button @click = "sendMessage" >Send</button>
    </div>
  </div>
</template>

<style scoped>

</style>
