<template>
  <div class="flex h-screen overflow-hidden">
    <!-- Sidebar -->
    <Sidebar :sidebar-open="sidebarOpen" @close-sidebar="sidebarOpen = false" />

    <!-- Content area -->
    <div
      class="relative flex flex-col flex-1 overflow-y-auto overflow-x-hidden"
    >
      <!-- Site header -->
      <Header
        :sidebar-open="sidebarOpen"
        @toggle-sidebar="sidebarOpen = !sidebarOpen"
      />

      <main>
        <div class="px-4 sm:px-6 lg:px-8 py-8 w-full max-w-9xl mx-auto">
          <!-- Page header -->
          <div class="sm:flex sm:justify-between sm:items-center mb-8">
            <!-- Left: Title -->
            <div class="mb-4 sm:mb-0">
              <h1 class="text-2xl md:text-3xl text-slate-800 font-bold">
                <i class="fas fa-users"></i> Users ✨
              </h1>
            </div>

            <!-- Right: Actions  -->
            <div
              class="grid grid-flow-col sm:auto-cols-max justify-start sm:justify-end gap-2"
            >
              <!-- Delete button -->
              <DeleteButton
                v-if="showDeleteButton"
                :selected-items="selectedItems"
                @click="onModaDeletelOpen"
              />
            </div>
          </div>

          <Banner v-if="updated" type="success" class="mb-4" :open="true">
            User information updated successfully.
          </Banner>

          <Banner v-if="deleted" type="success" class="mb-4" :open="true">
            User deleted successfully.
          </Banner>
          <!-- Table -->
          <CustomersTable
            @change-selection="updateSelectedItems($event)"
            @edit="onOpenModal"
          />

          <ModalBasic
            id="feedback-modal"
            :modal-open="modalOpen"
            title="Edit User"
            @close-modal="onModalClose"
          >
            <!-- Modal content -->
            <div class="px-5 py-4">
              <div class="space-y-3">
                <!--
                          <div role="status" v-if="loading">
                              <svg aria-hidden="true" class="w-8 h-8 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600" viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
                                  <path d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z" fill="currentColor"/>
                                  <path d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z" fill="currentFill"/>
                              </svg>
                              <span class="sr-only">Loading...</span>
                            </div>

                            -->
                <div class="grid grid-cols-2 gap-4">
                  <div class="col-span-1">
                    <label class="block text-sm font-medium mb-1" for="name"
                      >First Name<span class="text-rose-500">*</span></label
                    >
                    <input
                      id="name"
                      v-model="selectedItems.firstname"
                      class="form-input w-full px-2 py-1"
                      type="text"
                      required
                    />
                    <div v-if="errors.name" class="text-xs mt-1 text-rose-500">
                      {{ errors.name }}
                    </div>
                  </div>
                  <div class="col-span-1">
                    <label class="block text-sm font-medium mb-1" for="name"
                      >Last Name <span class="text-rose-500">*</span></label
                    >
                    <input
                      id="name"
                      v-model="selectedItems.lastname"
                      class="form-input w-full px-2 py-1"
                      type="text"
                    />
                    <div
                      v-if="errors.familyname"
                      class="text-xs mt-1 text-rose-500"
                    >
                      {{ errors.familyname }}
                    </div>
                  </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                  <div class="col-span-1">
                    <label class="block text-sm font-medium mb-1" for="name"
                      >Mobile <span class="text-rose-500">*</span></label
                    >
                    <input
                      id="name"
                      v-model="selectedItems.phoneNumber"
                      class="form-input w-full px-2 py-1"
                      type="text"
                      required
                    />
                    <div v-if="error" class="text-xs mt-1 text-rose-500">
                      {{ error }}
                    </div>
                  </div>
                  <div class="col-span-1">
                    <label class="block text-sm font-medium mb-1" for="name"
                      >Status <span class="text-rose-500">*</span></label
                    >
                    <select
                      v-model="selectedItems.isVerified"
                      class="form-select"
                    >
                      <option value="true">Actived</option>
                      <option value="false">Not Actived</option>
                    </select>
                  </div>
                </div>

                <div>
                  <label class="block text-sm font-medium mb-1" for="email"
                    >Email <span class="text-rose-500">*</span></label
                  >
                  <input
                    id="email"
                    v-model="selectedItems.email"
                    class="form-input w-full px-2 py-1"
                    type="email"
                    required
                  />
                  <div v-if="errors.email" class="text-xs mt-1 text-rose-500">
                    {{ errors.email }}
                  </div>
                  <div v-if="emailExist" class="text-xs mt-1 text-rose-500">
                    this mail already exists
                  </div>
                </div>
              </div>
            </div>
            <!-- Modal footer -->
            <div class="px-5 py-4 border-t border-slate-200">
              <div class="flex flex-wrap justify-end space-x-2">
                <button
                  class="btn-sm border-slate-200 hover:border-slate-300 text-slate-600"
                  @click.stop="
                    modalOpen = false;
                    showDeleteButton = false;
                  "
                >
                  Cancel
                </button>
                <button
                  class="btn-sm bg-indigo-500 hover:bg-indigo-600 text-white"
                  @click="updateUser"
                >
                  Edit
                </button>
              </div>
            </div>
          </ModalBasic>

          <ModalBasic id="danger-modal" :modal-open="modaDeletelOpen">
            <div class="p-5 flex w-full space-x-4">
              <!-- Icon -->
              <div
                class="w-10 h-10 rounded-full flex items-center justify-center shrink-0 bg-rose-100"
              >
                <svg
                  class="w-4 h-4 shrink-0 fill-current text-rose-500"
                  viewBox="0 0 16 16"
                >
                  <path
                    d="M8 0C3.6 0 0 3.6 0 8s3.6 8 8 8 8-3.6 8-8-3.6-8-8-8zm0 12c-.6 0-1-.4-1-1s.4-1 1-1 1 .4 1 1-.4 1-1 1zm1-3H7V4h2v5z"
                  />
                </svg>
              </div>
              <!-- Content -->
              <div>
                <!-- Modal header -->
                <div class="mb-2">
                  <div class="text-lg font-semibold text-slate-800">
                    Delete User?
                  </div>
                </div>
                <!-- Modal content -->
                <div class="text-sm mb-10">
                  <div class="">
                    <p>Are you sure you want to delete user?</p>
                  </div>
                </div>
                <!-- Modal footer -->
              </div>
            </div>

            <div class="flex flex-wrap justify-end space-x-2 m-6">
              <button
                class="btn-sm border-slate-200 hover:border-slate-300 text-slate-600"
                @click.stop="modaDeletelOpen = false"
              >
                Cancel
              </button>
              <button
                class="btn-sm bg-rose-500 hover:bg-rose-600 text-white"
                @click="deleteItem"
              >
                Yes, Delete it
              </button>
            </div>
          </ModalBasic>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import Sidebar from "@/partials/Sidebar.vue";
import Header from "@/partials/Header.vue";
import DeleteButton from "@/components/DeleteButton.vue";
import CustomersTable from "@/partials/dashboard/users/UsersTable.vue";
import { useStore } from "vuex";
import ModalBasic from "@/components/Modal.vue";
import axios from "axios";
import Banner from "@/components/Banner.vue";
import {
  phoneValidation,
  passwordValidation,
  emailValidation,
} from "@/utils/utils-common-function";

export default {
  name: "UserList",
  components: {
    Sidebar,
    Header,
    CustomersTable,
    DeleteButton,
    ModalBasic,
    Banner,
  },
  setup() {
    const showDeleteButton = ref(true);
    const store = useStore();
    const sidebarOpen = ref(false);
    const selectedItems = ref([]);
    const selectedItem = ref([]);
    const modalOpen = ref(false);
    const modaDeletelOpen = ref(false);
    const isVerifiedv = ref(false);
    const errors = ref({
      name: "",
      familyname: "",
      phoneNumber: "",
      email: "",
    });

    const error = ref(null);
    const emailExist = ref(false);
    const loading = ref(false);
    const updated = ref(false);
    const deleted = ref(false);

    const updateSelectedItems = (selected) => {
      selectedItems.value = selected;
      selectedItem.value = selected;
      showDeleteButton.value = true;
    };

    function onOpenModal(selected) {
      modalOpen.value = true;
      selectedItems.value = selected;
    }

    function onModaDeletelOpen(selected) {
      modaDeletelOpen.value = true;
    }

    async function deleteItem() {
      try {
        const response = await axios.delete(
          `${import.meta.env.VITE_API_URL}/auth/delete-user/${
            this.selectedItems[0]
          }`,
          {
            headers: {
              Authorization: `Bearer ${store.getters["auth/token"]}`,
            },
          }
        );
        modaDeletelOpen.value = false;
        deleted.value = true;
      } catch (e) {
        console.log(e);
        modaDeletelOpen.value = false;
        deleted.value = false;
      }
    }

    async function updateUser() {
      try {
        // Get the form data from the inputs

        if (!this.selectedItems.firstname) {
          errors.value.name = "Veuillez revérifier votre nom";
          return;
        }
        if (!this.selectedItems.lastname) {
          errors.value.familyname = "Veuillez revérifier votre prénom";
          return;
        }

        if (!emailValidation(this.selectedItems.email)) {
          errors.value.email =
            "Veuillez revérifier votre email s'il est valide";
          return;
        }

        if (!phoneValidation(this.selectedItems.phoneNumber)) {
          error.value =
            "Veuillez revérifier votre numéro de téléphone s'il est valide";
          return;
        }

        loading.value = true;
        const data = {
          id: this.selectedItems["_id"],
          email: this.selectedItems.email,
          firstname: this.selectedItems.firstname,
          lastname: this.selectedItems.lastname,
          isValide: this.selectedItems.isVerified == "true" ? true : false,
          phoneNumber: this.selectedItems.phoneNumber,
        };

        try {
          const token = store.getters["auth/token"];
          const response = await axios.put(
            `${import.meta.env.VITE_API_URL}/auth/update-user-admin/`,
            data,
            {
              headers: {
                Authorization: `Bearer ${token}`,
              },
            }
          );
        } catch (error) {
          if ((error.message = "Request failed with status code 500"))
            emailExist.value = true;
          return;
        }
        loading.value = false;
        updated.value = true;
        modalOpen.value = false;
      } catch (error) {
        console.log(error);
      }
    }

    return {
      sidebarOpen,
      selectedItems,
      selectedItem,
      modalOpen,
      updateSelectedItems,
      onOpenModal,
      modaDeletelOpen,
      updateUser,
      isVerifiedv,
      errors,
      error,
      emailExist,
      loading,
      updated,
      deleteItem,
      deleted,
      onModaDeletelOpen,
      showDeleteButton,
    };
  },
};
</script>
