<template>
  <div class="flex flex-col h-screen bg-gray-200">
    <div class="ventana-chat bg-white w-full max-w-md flex flex-col h-full">
      <div class="encabezado-chat bg-blue-600 text-white">
        <div class="contenido-encabezado flex items-center justify-between p-4">
          <h1 class="text-lg font-semibold">ChatBot ULS 2024</h1>
        </div>
      </div>

      <div class="cuerpo-chat flex-1 p-4 overflow-y-auto">
        <div v-if="cargando" class="text-center text-gray-500">Cargando...</div>

        <div
          v-for="(mensaje, index) in mensajes"
          :key="index"
          :class="[
            'mensaje',
            mensaje.remitente === 'usuario' ? 'mensaje-usuario' : 'mensaje-bot',
          ]"
        >
          <div v-if="mensaje.remitente === 'bot'" class="contenido-mensaje-bot">
            <p v-if="mensaje.texto" class="texto-mensaje">
              {{ mensaje.texto }}
            </p>
            <img
              v-if="mensaje.gif"
              :src="mensaje.gif"
              alt="Respuesta GIF"
              class="gif-mensaje"
            />
          </div>
          <p
            v-if="mensaje.remitente === 'usuario'"
            class="texto-mensaje texto-usuario"
          >
            {{ mensaje.texto }}
          </p>
        </div>
      </div>
      <div
        class="pie-chat bg-gray-100 border-t border-gray-300 flex items-center p-2"
      >
        <input
          v-model="mensajeUsuario"
          @keyup.enter="enviarMensaje"
          placeholder="Escribe un mensaje"
          type="text"
          class="flex-1 p-2 border rounded-full bg-blue-200 text-gray-800 placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-blue-400"
        />
        <button
          @click="enviarMensaje"
          class="ml-2 p-2 bg-green-500 text-white rounded-full hover:bg-green-600 transition"
        >
          ENVIAR
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

const mensajeUsuario = ref("");
const mensajes = ref<
  { gif: string | null; texto: string; remitente: "usuario" | "bot" }[]
>([]);
const cargando = ref(false);

const enviarMensaje = async () => {
  if (mensajeUsuario.value.trim() === "") {
    alert("Escribe algo antes de enviar");
    return;
  }

  mensajes.value.push({
    gif: null,
    texto: mensajeUsuario.value,
    remitente: "usuario",
  });
  mensajeUsuario.value = "";
  cargando.value = true;

  try {
    const res = await fetch("https://yesno.wtf/api");
    const datos = await res.json();
    mensajes.value.push({
      gif: datos.image,
      texto: datos.answer === "yes" ? "Sí" : "No",
      remitente: "bot",
    });
  } catch (error) {
    console.error("Error al obtener la API", error);
  } finally {
    cargando.value = false;
  }
};
</script>

<style scoped>
.ventana-chat {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.cuerpo-chat {
  flex: 1;
  padding: 1rem;
  overflow-y: auto;
}

.pie-chat {
  background-color: #f1f1f1;
  border-top: 1px solid #ddd;
  position: relative;
  bottom: 0;
  display: flex;
  align-items: center;
}

.pie-chat input {
  flex: 1;
  margin-right: 0.5rem;
  padding: 0.5rem 1rem;
  border-radius: 9999px;
}

.pie-chat button {
  flex-shrink: 0;
  padding: 0.5rem 1rem;
  background-color: #34d399;
  color: white;
  border-radius: 9999px;
  border: none;
  font-weight: bold;
  font-size: 0.875rem;
  cursor: pointer;
}

.pie-chat button:hover {
  background-color: #10b981;
}

.mensaje {
  margin-bottom: 1rem;
  display: flex;
  align-items: flex-end;
}

.mensaje-usuario {
  align-self: flex-end;
  margin-left: auto;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.mensaje-bot {
  background-color: #f0f0f0;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 1rem;
  max-width: 80%;
  border-radius: 12px;
  padding: 0.5rem;
  word-wrap: break-word;
}

.contenido-mensaje-bot {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  border-radius: 12px;
  background-color: #f0f0f0;
  padding: 0.5rem;
}

.texto-mensaje {
  font-family: "Roboto", sans-serif;
  text-align: left;
  padding: 0.5rem;
  border-radius: 12px;
  background-color: inherit;
}

.texto-usuario {
  background-color: #e0f7fa;
  border-radius: 12px;
  padding: 0.5rem;
  max-width: 70%;
  word-wrap: break-word;
}

.gif-mensaje {
  width: 100%;
  max-width: 200px;
  height: auto;
  border-radius: 12px;
}

.encabezado-chat {
  background-color: #4f7cf7;
  width: 100%;
  border-bottom: 1px solid #ddd;
}

.contenido-encabezado {
  width: 100%;
  color: rgb(255, 255, 255);
  text-align: center;
}
</style>
