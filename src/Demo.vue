<script setup lang="ts">
import { defineProps, defineEmits, withDefaults } from 'vue';

const props = withDefaults(
    defineProps<{
        prefix: string,
        person: {
            firstname: string,
            lastname: string
        }
    }>(), {
    prefix: 'Bonjour',
    person: () => ({
        firstname: 'John',
        lastname: 'Noé'
    })
});

const emit = defineEmits<{
    demo: { firstname: string, lastname: string }
}>();

const handleClick = () => {
    emit('demo', { firstname: props.person.firstname, lastname: props.person.lastname });
};
</script>

<template>
    <div>
        <p>
            {{ prefix }} {{ person.firstname }} {{ person.lastname }}
        </p>
        <button @click="handleClick">Cliquez ici</button>
    </div>
</template>
