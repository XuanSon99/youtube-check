<template>
  <div class="ss-container">
    <div class="ss-item ss-loop active" style="background-color: #fd4d0c">
      <div class="info">
        <v-data-table
          :headers="headers"
          :items="desserts"
          :search="search"
          sort-by="viewCount"
          update: sort-desc
          class="elevation-1"
        >
          <template v-slot:top>
            <v-toolbar flat>
              <v-toolbar-title><span style="color: #fff; text-shadow: 0 0 5px #000"><b>DANH SÁCH THỐNG KÊ</b></span></v-toolbar-title>
              <v-spacer></v-spacer>
              <div class="search-box">
                <v-text-field
                  v-model="search"
                  append-icon="mdi-magnify"
                  label="Tìm kiếm"
                  single-line
                  hide-details
                ></v-text-field>
              </div>
              <!-- <v-dialog v-model="dialog" max-width="500px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                    Thêm kênh
                  </v-btn>
                </template>
                <v-card>
                  <v-card-title>
                    <span class="headline">{{ formTitle }}</span>
                  </v-card-title>

                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12">
                          <v-text-field
                            v-model="editedItem.link"
                            label="Link kênh"
                          ></v-text-field>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>

                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="close"> Hủy </v-btn>
                    <v-btn color="blue darken-1" text @click="save"> Lưu </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog> -->
              <v-dialog v-model="dialogDelete" max-width="500px">
                <v-card>
                  <v-card-title class="headline"
                    >Are you sure you want to delete this item?</v-card-title
                  >
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="closeDelete"
                      >Cancel</v-btn
                    >
                    <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                      >OK</v-btn
                    >
                    <v-spacer></v-spacer>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-toolbar>
          </template>
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
            <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
          </template>
          <!-- <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
        </template> -->
        </v-data-table>
      </div>
    </div>
    <div class="ss-item ss-loop" style="background-color: #cbe432">
      <h1>2</h1>
    </div>
    <div class="ss-item ss-loop" style="background-color: #2e2fe3">
      <h1>3</h1>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      search: "",
      dialog: false,
      dialogDelete: false,
      headers: [
        { text: "Tài khoản", value: "account", sortable: false },
        {
          text: "Tên kênh",
          align: "start",
          sortable: false,
          value: "name",
        },
        { text: "Đăng ký", value: "subscriberCount" },
        { text: "Lượt xem", value: "viewCount" },
        { text: "Số video", value: "videoCount" },
        // { text: "Actions", value: "actions", sortable: false },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        link: "",
      },
      defaultItem: {
        link: "",
      },
      accountList: [],
      gmailList: [],
    };
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Thêm kênh" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      this.desserts = this.accountList;
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.desserts.splice(this.editedIndex, 1);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else {
        this.desserts.push(this.editedItem);
      }
      this.close();
    },

    formatNumber(value) {
      return String(value)
        .toString()
        .replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    },
  },
  mounted() {
    axios
      .get(
        "https://sheets.googleapis.com/v4/spreadsheets/1zgv5ogk5reWsfrAToiecQuhfaKM6nwyLSHp0eqgSBb8/values/Worksheet!1:130?key=AIzaSyAAUda1-y7m8HYDOrDBr_-rqdMtf9TJZRI"
      )
      .then((response) => {
        this.gmailList = response.data.values;
        for (let item of this.gmailList) {
          axios
            .get("https://www.googleapis.com/youtube/v3/channels", {
              params: {
                part: "statistics, snippet",
                key: "AIzaSyDh6jepQDGBbEeFhI-omOsUwea3hwn_-ew", //AIzaSyAyNYkNtBMdxgl6M5GIzP9s1LUVJjp2oKk
                id: item[1].slice(item[1].length - 24, item[1].length),
              },
            })
            .then((response) => {
              const profile = response.data.items[0];
              this.accountList.push({
                account: item[0],
                name: profile.snippet.title,
                subscriberCount: this.formatNumber(
                  profile.statistics.subscriberCount
                ),
                videoCount: this.formatNumber(profile.statistics.videoCount),
                viewCount: profile.statistics.viewCount,
              });
            });
        }
      });
    function scrollSlide(args) {
      //args.container, args.item, args.animType, args.duration, args.delay, args.uncutMove
      const scrollContainerEle = document.querySelector(args.container);
      const scrollItems = Array.from(
        document.querySelectorAll(`${args.container} ${args.item}`)
      );

      let allowAnimation = true;
      let allowAnimationTimeout;

      // NOTE:
      // INNER FUNCTIONS DECLARATION
      function addAnimationClasses() {
        scrollItems.forEach((item) => {
          item.classList.add(args.animType);
        });
      }

      function addLoppAnimClasses() {
        let activeItem = scrollItems.find((item) => {
          return item.classList.contains("active");
        });

        let nextItem = scrollItems[scrollItems.indexOf(activeItem) + 1];
        let prevItem = scrollItems[scrollItems.indexOf(activeItem) - 1];

        if (!nextItem) {
          nextItem = scrollItems[0];
        }

        if (!prevItem) {
          prevItem = scrollItems[scrollItems.length - 1];
        }
        let itemsProceed = 0;
        scrollItems.forEach((item) => {
          itemsProceed++;

          item.classList.remove("ss-move-prev");
          item.classList.remove("ss-move-next");

          if (itemsProceed === scrollItems.length) {
            nextItem.classList.add("ss-move-next");
            prevItem.classList.add("ss-move-prev");
          }
        });
      }

      function addAnimationDuration() {
        scrollItems.forEach((item) => {
          item.style.transitionDuration = `${args.duration}s`;
          addAnimationDelay(item);
        });
      }

      function addAnimationDelay(item) {
        item.style.transitionDelay = `${args.delay}s`;
      }

      function stopScrollAnim() {
        if (allowAnimation) {
          allowAnimation = false;
        }
        allowAnimationTimeout = setTimeout(() => {
          allowAnimation = true;

          scrollItems.forEach((item) => {
            item.classList.remove("ss-moving");
          });
        }, (args.duration + args.delay) * 1000);
      }

      function changeScrollSlide(moveDown) {
        let activeItem = scrollItems.find((item) => {
          return item.classList.contains("active");
        });
        let nextItem;

        if (moveDown) {
          nextItem = scrollItems[scrollItems.indexOf(activeItem) + 1];
        } else {
          nextItem = scrollItems[scrollItems.indexOf(activeItem) - 1];
        }

        if (nextItem) {
          activeItem.classList.add("ss-moving");
          nextItem.classList.add("ss-moving");
          activeItem.classList.remove("active");
          nextItem.classList.add("active");

          if (args.uncutMove) {
            addLoppAnimClasses();
          }
        } else {
          activeItem.classList.add("ss-moving");
          activeItem.classList.remove("active");

          if (moveDown) {
            scrollItems[0].classList.add("ss-moving");
            scrollItems[0].classList.add("active");

            if (args.uncutMove) {
              addLoppAnimClasses();
            }
          } else {
            scrollItems[scrollItems.length - 1].classList.add("ss-moving");
            scrollItems[scrollItems.length - 1].classList.add("active");

            if (args.uncutMove) {
              addLoppAnimClasses();
            }
          }
        }
      }

      // NOTE:
      // INNER FUNCTION CALLS
      addAnimationClasses();
      if (args.uncutMove) {
        addLoppAnimClasses();
      }
      addAnimationDuration();

      return (function () {
        let eventType;
        let isFirefox =
          navigator.userAgent.toLowerCase().indexOf("firefox") > -1;
        let isIe = /MSIE|Trident/.test(window.navigator.userAgent);
        if (isFirefox) {
          eventType = "DOMMouseScroll";
        } else if (isIe) {
          eventType = "mousewheel";
        } else {
          eventType = "wheel";
        }
        scrollContainerEle.addEventListener(eventType, function (event) {
          let scrollTop = scrollContainerEle.scrollTop,
            scrollHeight = scrollContainerEle.scrollHeight,
            height = scrollContainerEle.clientHeight;

          let delta = event.wheelDelta
            ? event.wheelDelta
            : -(event.detail || 0);
          if (
            (delta > 20 && scrollTop - delta <= 0) ||
            (delta < -20 && scrollTop + height >= scrollHeight - 1)
          ) {
            if (delta > 20) {
              if (allowAnimation) {
                stopScrollAnim();
                changeScrollSlide(false);
              }
            } else {
              if (allowAnimation) {
                stopScrollAnim();
                changeScrollSlide(true);
              }
            }
            event.preventDefault();
          } else {
            if (delta < -20) {
              if (allowAnimation) {
                stopScrollAnim();
                changeScrollSlide(true);
              }
            }
            event.preventDefault();
          }
        });
      })();
    }
    scrollSlide({
      container: ".ss-container",
      item: ".ss-item",
      animType: "ss-move-left",
      duration: 1,
      delay: 0,
      uncutMove: true,
    });
  },
};
</script>
<style>
.info{
  width: 100%;
}
.ss-container{
  height: 100vh;
  width: 100vw;
}
@media (min-width: 480px) {
  .v-sheet {
    background: none !important;
  }
  .info {
    background: linear-gradient(to bottom right, #003366 0%, #ff0066 100%);
    height: 100vh;
    overflow: hidden;
  }
  .v-data-table {
    background: rgba(255, 255, 255, 0.4) !important;
    margin: 0 auto;
    height: 100vh;
  }
  .v-data-footer {
    background: none !important;
  }
  .theme--light.v-text-field:not(.v-input--has-state):hover
    > .v-input__control
    > .v-input__slot:before {
    display: none !important;
  }
  .theme--light.v-text-field > .v-input__control > .v-input__slot:before {
    display: none !important;
  }
  tbody tr:hover {
    background: linear-gradient(
      to bottom right,
      #7182a3 0%,
      #e86ba4 100%
    ) !important;
  }
  .search-box {
    margin-right: 20px;
  }
}
@media (max-width: 480px) {
  .v-toolbar__title {
    display: none;
  }
  .spacer {
    display: none;
  }
  .search-box {
    width: 100%;
  }
  .info {
    background: linear-gradient(to bottom right, #003366 0%, #ff0066 100%);
  }
  .v-data-table {
    background: rgba(255, 255, 255, 0.4) !important;
    min-height: 100vh;
  }
  .v-data-footer {
    background: none !important;
  }
}
</style>

