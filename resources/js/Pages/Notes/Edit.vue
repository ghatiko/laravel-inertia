<template>
    <AppLayout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Edit note
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="md:grid md:grid-cols-3 md:gap-6">
                    <div class="md:col-span-1">
                        <div class="px4 sm:px0">
                            <h3 class="text-lg text-gray-900">Edit an note</h3>
                            <p class="text-sm text-gray-600">Si editar no podrás volver al estado anterior.</p>
                        </div>
                    </div>
                    <div class="md:col-span-2 mt-5 md:mt-0">
                        <span class="mt-3 py-5 border-t-2 block">
                            <span class="mr-2">
                                <SecondaryButton :href="route('notes.index')">
                                    Back
                                </SecondaryButton>
                            </span>

                            <span class="mr-2">
                                <WarningButton :href="route('notes.show', note.id)">
                                    Show
                                </WarningButton>
                            </span>
                            <span>
                                <inertia-link @click.prevent="destroy(note.id)"
                                    class="text-red-900 hover:text-white border border-red-800 hover:bg-red-900 focus:ring-4 focus:outline-none focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2 dark:border-red-600 dark:text-red-400 dark:hover:text-white dark:hover:bg-red-600 dark:focus:ring-red-800">
                                    Eliminar
                                </inertia-link>
                            </span>
                        </span>
                        <div class="shadow bg-white md:rounded-md p-4">
                            <form @submit.prevent="submit">
                                <div class="flex flex-wrap -mx-3 mb-6">
                                    <div class="w-full px-3">
                                        <label
                                            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                                            for="grid-first-name">
                                            Title
                                        </label>
                                        <input v-model="form.title"
                                            class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                                            id="grid-first-name" type="text" placeholder="Jane" />
                                        <div v-if="errors.title" class="text-red-800 mt-3">{{ errors.title }}</div>
                                    </div>

                                    <div class="w-full px-3 my-5">
                                        <label
                                            class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                                            for="grid-last-name">
                                            Body
                                        </label>
                                        <textarea v-model="form.body"
                                            class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                                            id="grid-last-name" placeholder="..."></textarea>
                                        <div v-if="errors.body" class="text-red-900 mt-3">{{ errors.body }}</div>
                                    </div>

                                    <div class="w-full px-3 mt-5">
                                        <div>
                                            <progress v-if="form.progress" :value="form.progress.percentage" max="100"
                                                class="w-full">
                                                {{ form.progress.percentage }}%
                                            </progress>
                                        </div>
                                        <!-- button submit -->
                                        <button type="submit" :disabled="form.processing"
                                            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline">
                                            Guardar
                                        </button>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>

<script>
import AppLayout from '@/Layouts/AppLayout.vue';
import { reactive } from 'vue'
import { Inertia } from '@inertiajs/inertia'
import SecondaryButton from '@/Jetstream/SecondaryButton.vue';
import WarningButton from '@/JetStream/WarningButton.vue';

export default {
    components: {
        AppLayout,
        SecondaryButton,
        WarningButton
    },
    props: {
        note: {
            type: Object,
            required: true,
        },
        errors: {
            type: Object,
            default: () => ({}),
        },
    },
    setup(props) {
        
        //form reactive data
        const form = reactive({
            title: props.note.title,
            body: props.note.body,
            processing: false,
            progress: null,
            errors: {
                title: null,
                body: null,
            },
        });

        //Method: submit
        const submit = () => {
            form.processing = true
            form.progress = {
                percentage: 0,
            }

            const interval = setInterval(() => {
                form.progress.percentage += 10;

                if (form.progress.percentage >= 100) {
                    clearInterval(interval);
                    form.processing = false
                    form.progress = null
                }
            }, 100);

            //Http request
            Inertia.put('/notes/' + props.note.id, form);
        }

        //Method: destroy
        const destroy = (id) => {
            // delete note
            if (confirm('Seguro que desea eliminar la nota?')) {
                Inertia.delete('/notes/' + id);
            }
        };

        //Return data
        return {
            form,
            submit,
            destroy,
        };
    }
}

</script>
