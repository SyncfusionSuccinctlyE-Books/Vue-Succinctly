<template>
    <div>
        <v-dialog v-model="showModal"
          fullscreen
          hide-overlay
          transition="dialog-bottom-transition"
          scrollable
        >
            <v-card tile>
                <v-toolbar color="blue" dark>
                <v-btn icon dark @click="hide">
                    <v-icon>close</v-icon>
                </v-btn>
                <v-toolbar-title>{{item.name}}</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-toolbar-items>
                    <v-btn dark flat @click="save">Save</v-btn>
                </v-toolbar-items>
                </v-toolbar>
                <v-card-text>
                <v-list three-line subheader>
                    <v-list-tile avatar>
                    <v-list-tile-content>
                        <v-list-tile-title>Document Name</v-list-tile-title>
                        <v-list-tile-sub-title></v-list-tile-sub-title>
                        <v-text-field
                            ref="name"
                            v-model="item.name"
                            :rules="[() => !!item.name || 'Required field']"
                            required
                            >
                        </v-text-field>
                    </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile avatar>
                    <v-list-tile-content>
                        <v-list-tile-title>Expiry Date</v-list-tile-title>
                        <v-list-tile-sub-title></v-list-tile-sub-title>
                        <v-menu
                            ref="menu"
                            v-model="menu"
                            :close-on-content-click="false"
                            :nudge-right="40"
                            :return-value.sync="date"
                            lazy
                            transition="scale-transition"
                            offset-y
                            full-width
                            min-width="290px"
                            >
                            <template v-slot:activator="{ on }">
                                <v-text-field
                                v-model="date"
                                prepend-icon="event"
                                readonly
                                v-on="on"
                                ></v-text-field>
                            </template>
                            <v-date-picker 
                                v-model="date"
                                :min=today
                                max="2099-12-31"
                                no-title scrollable>
                                <v-spacer></v-spacer>
                                <v-btn flat color="primary" @click="menu = false">Cancel</v-btn>
                                <v-btn flat color="primary" @click="$refs.menu.save(date)">OK</v-btn>
                            </v-date-picker>
                        </v-menu>
                    </v-list-tile-content>
                    </v-list-tile>
                </v-list>
                <v-divider></v-divider>
                <v-list four-line subheader>
                    <v-list-tile avatar>
                        <v-list-tile-action>
                            <v-switch
                                v-model="item.alert1year"
                                :label="`Alert @ 1.5 & 1 year(s)`"
                            ></v-switch>
                        </v-list-tile-action>
                    </v-list-tile>
                    <v-list-tile avatar>
                        <v-list-tile-action>
                            <v-switch
                                v-model="item.alert6months"
                                :label="`Alert @ 6 months`"
                            ></v-switch>
                        </v-list-tile-action>
                    </v-list-tile>
                    <v-list-tile avatar>
                        <v-list-tile-action>
                            <v-switch
                                v-model="item.alert3months"
                                :label="`Alert @ 3 months`"
                            ></v-switch>
                        </v-list-tile-action>
                    </v-list-tile>
                    <v-list-tile avatar>
                        <v-list-tile-action>
                            <v-switch
                                v-model="item.alert1month"
                                :label="`Alert @ 1 month or less`"
                            ></v-switch>
                        </v-list-tile-action>
                    </v-list-tile>
                </v-list>
                </v-card-text>
    
                <div style="flex: 1 1 auto;"></div>
            </v-card>
        </v-dialog>
    </div>
</template>

<script>
import sheetsu from 'sheetsu-node';

let db = sheetsu({ 
  address: "https://sheetsu.com/apis/v1.0su/05755177b2fb"});

export default {
    name: "Doc",
    props: ["item", "items"],
    data: () => {
        return {
            name: "",
            date: new Date().toISOString().substr(0, 10),
            today: new Date().toISOString().substr(0, 10),
            menu: false,
            showModal: false
        }
    },
    methods: {
        show() {
            this.showModal = true;
            if (this.item.id > -1) {
                this.date = this.item.exp;
            }
        },
        hide(){
            this.showModal = false;
        },
        save(){
            if (this.item.id == - 1) {
                this.adddoc();
            }
            else {
                this.editdoc();
            }
            this.showModal = false;
        },
        adddoc() {
            let doc = {
                id: this.items.length + 1,
                name: this.item.name,
                exp: this.date,
                alert1year: this.item.alert1year,
                alert6months: this.item.alert6months,
                alert3months: this.item.alert3months,
                alert1month: this.item.alert1month
            };
            db.create(doc).then(() => {
                this.items.push(doc);
            });
        },
        editdoc() {
            this.item.exp = this.date;
            let doc = { 
                id: this.item.id,
                name: this.item.name,
                exp: this.date,
                alert1year: this.item.alert1year,
                alert6months: this.item.alert6months,
                alert3months: this.item.alert3months,
                alert1month: this.item.alert1month };
            db.update(
                "id", 
                this.item.id,
                doc).then(() => {
                let idx = this.items.findIndex((itm => itm.id == this.item.id));
                this.items[idx] = doc;
            });
        }
    }
  }
</script>

<style scoped>
</style>
