<template>
    <div>
        <v-toolbar color="blue" dark>
            <v-toolbar-title>DocExpire</v-toolbar-title>
            <v-spacer></v-spacer>
        </v-toolbar>

        <v-list two-line>
            <template v-for="item in items">
                <Item :key="item.id" :lgth="items.length" :item="item" :items="items"/>
            </template>
        </v-list>
    </div>
</template>

<script>
import Item from './Item';

export default {
    name: "Docs",
    props: ["items"],
    components: {
        Item
    }
}
</script>

<style scoped>
</style>