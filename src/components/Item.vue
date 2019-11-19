<template>
    <div>
        <Doc ref="modal" :item="item" :items="items" />
        <v-list-tile avatar ripple>
            <v-btn @click="removeItem" flat icon color="red lighten-2">
                <v-icon>delete_forever</v-icon>
            </v-btn>
            <v-btn @click="openModal" flat icon color="green lighten-2">
                <v-icon>edit</v-icon>
            </v-btn>
            <v-list-tile-content>
                <v-list-tile-title class="text--primary">{{ item.name }}</v-list-tile-title>
                <v-list-tile-sub-title>{{ item.exp }}</v-list-tile-sub-title>
                <v-list-tile-sub-title>{{daysLeft(item.exp)}}</v-list-tile-sub-title>
            </v-list-tile-content>
            <v-list-tile-action>
                <v-list-tile-action-text>{{ item.id }}</v-list-tile-action-text>
                <v-icon v-if="!hasExpired(item.exp)" color="green lighten-1">done_outline</v-icon>
                <v-icon v-else color="red lighten-1">warning</v-icon>
            </v-list-tile-action>
        </v-list-tile>
        <v-divider v-if="item.id + 1 < lgth" :key="`divider-${item.id}`"></v-divider>
    </div>
</template>

<script>
import Doc from './Doc';
import moment from 'moment';
import sheetsu from 'sheetsu-node';

let db = sheetsu({ 
  address: "https://sheetsu.com/apis/v1.0su/05755177b2fb"});

export default {
    name: "Item",
    components: {
        Doc
    },
    props: ["item", "lgth", "items"],
    methods: {
        openModal() {
            this.$refs.modal.show();
        },
        removeItem() {
            db.delete(
                "id",                
                this.item.id
            ).then(() => {
                let idx = this.items.indexOf(this.item);
                if (idx > -1) {
                    this.items.splice(idx, 1);
                }
            });
        },
        daysLeft(dt) {
            let ends = moment(dt);
            let starts = moment(Date.now());
            let years = ends.diff(starts, "year");
            starts.add(years, "years");
            let months = ends.diff(starts, "months");
            starts.add(months, "months");
            let days = ends.diff(starts, "days");
            let rs = years + " years " + months + " months " + days + " days";
            return this.hasExpired(dt) ? "Expired " + rs.replace(/-/g, '') + " ago": "Left: " + rs;
        },
        hasExpired(dt) {
            return (Date.parse(dt) <= Date.now()) ? true : false;
        }
    }
}
</script>

<style scoped>
</style>