<template>
  <app-layout title="Chat">
    <template #header>
      <h2 class="font-semibold text-xl text-gray-600 leading-tight">Chat</h2>
    </template>
    <div class="py-2">
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <jet-input
          type="text"
          class="mt-1 block w-full"
          placeholder="Message"
          ref="text"
          v-model="form.message"
          @keyup.enter="submit"
        />
        <ul>
          <li v-for="row in messages" v-bind:key="row.id">
            <div
              class="
                transform
                transition
                cursor-pointer
                hover:-translate-y-2
                mt-5
                ml-10
                relative
                flex
                items-center
                px-6
                py-4
                bg-indigo-600
                text-white
                rounded
                mb-10
                flex-col
                md:flex-row
                space-y-4
                md:space-y-0
              "
            >
              <!-- Dot Follwing the Left Vertical Line -->
              <div
                class="
                  w-5
                  h-5
                  bg-indigo-600
                  absolute
                  -left-10
                  transform
                  -translate-x-2/4
                  rounded-full
                  z-10
                  mt-2
                  md:mt-0
                "
              ></div>

              <!-- Line that connecting the box with the vertical line -->
              <div class="w-10 h-1 bg-indigo-300 absolute -left-10 z-0"></div>

              <!-- Content that showing in the box -->
              <div class="flex-auto">
                <h1 class="text-sm">{{ row.name }}</h1>
                <h1 class="text-md font-bold">{{ row.message }}</h1>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "@/Layouts/AppLayout";
import JetInput from "@/Jetstream/Input";
import { onMounted, reactive, ref } from "vue";
import { Inertia } from "@inertiajs/inertia";
export default {
  name: "Chats",
  components: {
    AppLayout,
    JetInput,
  },
  props: {
    messages: Object,
    errors: Object,
  },
  setup(props) {
    const msg = ref(null);
    const form = reactive({
      name : null,
      message: null,
    });
    function submit() {
     
        Inertia.post("/chat", form, {
          preserveState: true,
          onSuccess: (page) => {
            console.log("success");
          },
          onError: (errors) => {
            console.log("error ..".errors);
          },
        });
    }
    onMounted(()=>{
         Echo.private('chat')
            .listen('MessageSent', (e) => {
              console.log(e);
                props.messages.push({
                message: e.message.message,
                name: e.user.name
              });
            });
        
    });
    return { form,msg,submit };
  }
};
</script>

