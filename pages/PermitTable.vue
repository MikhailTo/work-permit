<template>
	<v-data-table
    :headers="headers"
    :items="permits"
    sort-by="calories"
    class="elevation-1"
  >
    <template #top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Наряды-допуски</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="1000px"
        >
          <template #activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              Создать
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                    	v-model="editedItem.accountable_name"
                    	:items="employees"
						label="Ответственный"
						dense
          				outlined
                    ></v-select>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      	v-model="editedItem.allower_name"
					  	:items="employees"
                      	label="Допускающий"
					  	dense
          				outlined
                    ></v-select>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.watching_name"
					  :items="employees"
                      label="Наблюдающий"
					  dense
          				outlined
                    ></v-select>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.issuer_name"
					  :items="employees"
                      label="Выдающий"
					  dense
          				outlined
                    ></v-select>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.producer_name"
					  :items="employees"
                      label="Производитель"
					  dense
          				outlined
                    ></v-select>
                  </v-col>
				  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
				<v-select
      				v-model="editedItem.brigade_members"
      				:items="employees"
      				label="Члены бригады"
      				multiple
					dense
          			outlined
    				>
    			</v-select>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Отмена
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Сохранить
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Вы точно уверены?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Нет</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">Да</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template #[`item.actions`]="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template #no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
  export default {
    data: () => ({
      dialog: false,
      dialogDelete: false,
      headers: [
        { text: 'Выдающий', align: 'start', value: 'issuer_name' },
        { text: 'Производитель', value: 'producer_name' },
		{ text: 'Члены бригады', value: 'brigade_members' },
        { text: 'Допускающий', value: 'allower_name' },
		{ text: 'Ответственный', value: 'accountable_name' },
		{ text: 'Наблюдающий', value: 'watching_name' },
        { text: 'Действия', value: 'actions', sortable: false },
      ],
      permits: [],
      editedIndex: -1,
      editedItem: {
        		issuer_name: "",
				accountable_name: "",
				allower_name: "",
				producer_name: "",
				watching_name: "",
				brigade_members: "",
				errand: "",
				add_instrs: "",
				extrad_date: null,
				start_date: null,
				end_date: null,
				interventions: [{
					installation_name: "",
					disconnections: "",
					protections: ""	
				}],
      },
      defaultItem: {
        		issuer_name: "",
				accountable_name: "",
				allower_name: "",
				producer_name: "",
				watching_name: "",
				brigade_members: "",
				errand: "",
				add_instrs: "",
				extrad_date: null,
				start_date: null,
				end_date: null,
				interventions: [{
					installation_name: "",
					disconnections: "",
					protections: ""	
				}]
      },
	  employees: [" ", "не назначается", "Оперативный персонал", "Шутов М.А. IV гр.", "Тошкин М.А. III гр.",
					"Поляков А.Ю. IVгр.,", "Лестин С.. IVгр.,", "Крапивин В.. IVгр.,", "Мягков В.Н. Vгр."],
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Создание' : 'Редактирование'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      initialize () {
        this.permits = [
          {
            id: 1,
      		issuer_name: "Мягков В.Н. Vгр.",
      		accountable_name: "не назначается",
      		allower_name: "оперативному персоналу",
      		producer_name: "Шутов М.А. IV гр.",
      		watching_name: "",
      		brigade_members: [
        		"Тошкин М.А. III гр."
      ],
          },
          {
			id: 2,
			issuer_name: "Мягков В.Н. Vгр.",
      		accountable_name: "не назначается",
      		allower_name: "оперативному персоналу",
      		producer_name: "Поляков А.Ю. IVгр.",
      		watching_name: "",
      		brigade_members: [
        		"Чижонков С.А.  IVгр."
      		],
          },
        ]
      },

      editItem (item) {
        this.editedIndex = this.permits.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = this.permits.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.permits.splice(this.editedIndex, 1)
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.permits[this.editedIndex], this.editedItem)
        } else {
          this.permits.push(this.editedItem)
        }
        this.close()
      },
    },
  }
</script>
