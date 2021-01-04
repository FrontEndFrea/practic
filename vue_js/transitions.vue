<template>
  <div id="app">
    <div class="container">
      <div class="header box">
        <div class="header-item icon">
          <a></a>
        </div>
        <div
          class="header-item header-btn search"
          :class="{ expanded: searchExpanded === true }"
        >
          <input
            type="text"
            placeholder="Search..."
            @blur="toggleSearch"
            @keydown="toggleCancel"
            @keyup="toggleCancel"
            ref="searchBar"
            v-model="searchContent"
          />
          <button
            class="search-cancel"
            :class="{ shown: showCancel }"
            @click="clearSearch"
          >
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M19,6.41L17.59,5L12,10.59L6.41,5L5,6.41L10.59,12L5,17.59L6.41,19L12,13.41L17.59,19L19,17.59L13.41,12L19,6.41Z"
              />
            </svg>
          </button>
          <button class="btn-search" @click="toggleSearch">
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M9.5,3A6.5,6.5 0 0,1 16,9.5C16,11.11 15.41,12.59 14.44,13.73L14.71,14H15.5L20.5,19L19,20.5L14,15.5V14.71L13.73,14.44C12.59,15.41 11.11,16 9.5,16A6.5,6.5 0 0,1 3,9.5A6.5,6.5 0 0,1 9.5,3M9.5,5C7,5 5,7 5,9.5C5,12 7,14 9.5,14C12,14 14,12 14,9.5C14,7 12,5 9.5,5Z"
              />
            </svg>
          </button>
        </div>
      </div>
      <div class="menu box">
        <div class="menu-item-container">
          <transition name="change-icon" mode="out-in">
            <button
              class="menu-item"
              @click="changeMenu('Add')"
              v-if="deletes.length === 0"
            >
              <!--From https://materialdesignicons.com/-->
              <svg viewBox="0 0 24 24">
                <path d="M19,13H13V19H11V13H5V11H11V5H13V11H19V13Z" />
              </svg>
            </button>
          </transition>
          <transition name="change-icon" mode="out-in">
            <button
              class="menu-item"
              @click="changeMenu('Delete')"
              v-if="deletes.length > 0"
            >
              <!--From https://materialdesignicons.com/-->
              <svg viewBox="0 0 24 24">
                <path
                  d="M19,4H15.5L14.5,3H9.5L8.5,4H5V6H19M6,19A2,2 0 0,0 8,21H16A2,2 0 0,0 18,19V7H6V19Z"
                />
              </svg>
            </button>
          </transition>
        </div>
      </div>
      <div class="content notes">
        <transition-group name="note" tag="div">
          <div
            class="note"
            v-for="(note, index) in notes"
            v-if="
              note.Title.toLowerCase().indexOf(searchContent.toLowerCase()) >= 0
            "
            :key="index"
          >
            <a
              href="#"
              class="note-link"
              @click.prevent="changeMenu('Edit', index)"
            >
              <div class="note-title">
                {{ note.Title }}
              </div>
              <div class="note-content">{{ note.Content }}</div>
            </a>
            <a
              href="#"
              class="delete-link"
              @click.prevent="toggleDeleteItem(index)"
            >
              <transition name="change-icon" mode="out-in">
                <!--From https://materialdesignicons.com/-->
                <svg viewBox="0 0 24 24" v-if="deletes.indexOf(index) < 0">
                  <path
                    d="M19,4H15.5L14.5,3H9.5L8.5,4H5V6H19M6,19A2,2 0 0,0 8,21H16A2,2 0 0,0 18,19V7H6V19Z"
                  />
                </svg>
              </transition>
              <transition name="change-icon" mode="out-in">
                <!--From https://materialdesignicons.com/-->
                <svg viewBox="0 0 24 24" v-if="deletes.indexOf(index) >= 0">
                  <path
                    d="M21,7L9,19L3.5,13.5L4.91,12.09L9,16.17L19.59,5.59L21,7Z"
                  />
                </svg>
              </transition>
            </a>
          </div>
        </transition-group>
      </div>
    </div>
    <transition name="window" mode="out-in">
      <div
        class="window"
        v-if="menu === getMenu('Add') || menu === getMenu('Edit')"
      >
        <div class="header box">
          <button
            class="header-item header-btn header-window"
            :class="{ expanded: searchExpanded === true }"
            @click="closeWindow"
          >
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M19,6.41L17.59,5L12,10.59L6.41,5L5,6.41L10.59,12L5,17.59L6.41,19L12,13.41L17.59,19L19,17.59L13.41,12L19,6.41Z"
              />
            </svg>
          </button>
        </div>
        <div class="menu box">
          <button class="menu-item" @click="confirmNote">
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M21,7L9,19L3.5,13.5L4.91,12.09L9,16.17L19.59,5.59L21,7Z"
              />
            </svg>
          </button>
        </div>
        <div class="content form">
          <div class="form-item form-title">
            <input
              type="text"
              placeholder="Enter title here..."
              v-model="noteTitle"
            />
          </div>
          <div class="form-item form-content">
            <textarea
              placeholder="Enter content here..."
              v-model="noteContent"
            ></textarea>
          </div>
        </div>
      </div>
    </transition>
    <transition name="window" mode="out-in">
      <div class="window" v-if="menu === getMenu('Delete')">
        <div class="header box">
          <button
            class="header-item header-btn header-window"
            :class="{ expanded: searchExpanded === true }"
            @click="closeWindow"
          >
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M19,6.41L17.59,5L12,10.59L6.41,5L5,6.41L10.59,12L5,17.59L6.41,19L12,13.41L17.59,19L19,17.59L13.41,12L19,6.41Z"
              />
            </svg>
          </button>
        </div>
        <div class="menu box">
          <button class="menu-item" @click="deleteNote">
            <!--From https://materialdesignicons.com/-->
            <svg viewBox="0 0 24 24">
              <path
                d="M21,7L9,19L3.5,13.5L4.91,12.09L9,16.17L19.59,5.59L21,7Z"
              />
            </svg>
          </button>
        </div>
        <div class="content">
          <div class="text">
            Selected item{{ ((deletes.length) > 1 ? "s" : "") }} will be deleted
            permanently.
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
const Menu = {
  Top: 0,
  Add: 1,
  Edit: 2,
  Delete: 3
};

export default {
  data() {
    return {
      menu: Menu.Top,
      searchExpanded: false,
      showCancel: false,
      noteTitle: "",
      noteContent: "",
      searchContent: "",
      edit: null,
      notes: [],
      deletes: []
    };
  },
  mounted() {
    this.notes.push({
      Title: "Initial Example",
      Content:
        "This is the content of my initial example, this text must be veeeery long."
    });

    this.notes.push({
      Title: "Second note",
      Content: "Content is short."
    });
  },
  methods: {
    getMenu(menu) {
      if (typeof Menu[menu] !== "undefined") {
        return Menu[menu];
      }

      return null;
    },
    toggleSearch() {
      if (this.searchContent !== "") {
        this.searchExpanded = true;

        return;
      }

      this.searchExpanded = !this.searchExpanded;

      if (this.searchExpanded === true) {
        this.$refs.searchBar.focus();
      }
    },
    toggleCancel() {
      if (this.searchContent !== "") {
        this.showCancel = true;

        return;
      }

      this.showCancel = false;
    },
    clearSearch() {
      if (this.searchContent === "") {
        return;
      }

      this.searchContent = "";
      this.showCancel = false;

      this.$refs.searchBar.focus();
    },
    changeMenu(menu, index = null) {
      if (typeof Menu[menu] !== "undefined") {
        if (Menu[menu] === Menu.Add) {
          if (this.deletes.length > 0) {
            return;
          }

          this.edit = null;
        } else if (Menu[menu] === Menu.Edit) {
          if (this.deletes.length > 0) {
            return;
          }

          this.edit = index;

          this.noteTitle = this.notes[index].Title;
          this.noteContent = this.notes[index].Content;
        }

        this.menu = Menu[menu];
      }
    },
    closeWindow() {
      this.menu = Menu.Top;
      this.noteTitle = "";
      this.noteContent = "";
    },
    confirmNote() {
      if (this.edit !== null) {
        this.notes[this.edit].Title = this.noteTitle;
        this.notes[this.edit].Content = this.noteContent;

        this.closeWindow();

        return;
      }

      if (this.noteTitle === "") {
        this.noteTitle = "Untitled";
      }

      this.notes.push({
        Title: this.noteTitle,
        Content: this.noteContent
      });

      this.closeWindow();
    },
    toggleDeleteItem(index) {
      let deleteIndex = this.deletes.indexOf(index);

      if (deleteIndex < 0) {
        this.deletes.push(index);
      } else {
        this.deletes.splice(deleteIndex, 1);
      }
    },
    deleteNote() {
      for (let i = 0; i < this.deletes.length; i++) {
        this.notes.splice(this.deletes[i], 1);
      }

      this.deletes = [];

      this.closeWindow();
    }
  }
};
</script>

<style lang="scss">
$padding-x: 0.8em;
$padding-y: 0.5em;
$border-size: 0.1em;
$border-color: #000000;

$header-height: 3.5em;

$icon-size: 2.8em;

$header-btn-size: 2.2em;

$search-size: $header-btn-size - ($border-size * 2);

$search-cancel-width: 1.8em;

$menu-height: 2.5em;

$menu-item-size: 2em;

@import url("https://fonts.googleapis.com/css?family=Open+Sans:400,400i,700");

button,
input[type="text"],
textarea {
  font-family: inherit;
  font-size: inherit;
  border: 0;
  padding: 0;
  outline: 0;
  margin: 0;
  svg {
    width: 80%;
    height: 80%;
    margin: 0;
    padding: 0;
    vertical-align: top;
  }
}

#app {
  font-family: "Open Sans", sans-serif;
  font-size: 18px;
  width: 100vw;
  height: 100vh;
}

.container,
.window {
  background-color: #ffffff;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.window {
  position: absolute;
  top: 0;
  left: 0;
  box-shadow: 0 0 1em 0.3em rgba(#000000, 0.3);
  &.window-enter-active,
  &.window-leave-active {
    transition: opacity 250ms ease-out, left 250ms ease-out;
  }
  &.window-enter,
  &.window-leave-to {
    left: -100%;
    opacity: 0;
  }
}

.box {
  width: 100%;
  padding: 0 $padding-x;
  border-bottom: solid $border-size $border-color;
  box-sizing: border-box;
  display: flex;
  flex-grow: 0;
  flex-shrink: 0;
  align-items: center;
}

.header {
  height: $header-height;
  .header-item {
    border: solid $border-size $border-color;
    box-sizing: border-box;
    overflow: hidden;
    border-radius: 50%;
    &.header-btn {
      width: $header-btn-size;
      height: $header-btn-size;
      &:not(.header-window) {
        margin-left: auto;
        margin-right: $padding-x;
        &:last-child {
          margin-right: 0;
        }
      }
    }
  }
  .icon {
    background: #c1c1c1;
    width: $icon-size;
    height: $icon-size;
  }
  .search {
    display: flex;
    justify-content: flex-end;
    flex-wrap: nowrap;
    overflow: hidden;
    border-radius: $header-btn-size / 2;
    transition: width 200ms ease-out;
    &.expanded {
      width: calc(100% - #{$icon-size + $padding-x});
    }
    .btn-search {
      width: $search-size;
      height: $search-size;
      flex-grow: 0;
      flex-shrink: 0;
    }
    input[type="text"] {
      width: calc(100% - #{$search-size + $padding-x + $search-cancel-width});
      height: $search-size;
      padding: 0 0 0 $padding-x;
      flex-grow: 0;
      flex-shrink: 0;
    }
    .search-cancel {
      background-color: transparent;
      width: $search-cancel-width;
      height: 100%;
      flex-grow: 0;
      flex-shrink: 0;
      opacity: 0;
      transition: opacity 150ms ease-out;
      &.shown {
        opacity: 1;
      }
    }
  }
}

.menu {
  background-color: #efefef;
  width: 100%;
  height: $menu-height;
  padding: 0 $padding-x;
  box-sizing: border-box;
  justify-content: flex-end;
  .menu-item-container {
    width: $menu-item-size;
    height: $menu-item-size;
    position: relative;
    .menu-item {
      position: absolute;
      top: 0;
      left: 0;
      opacity: 1;
    }
  }
  .menu-item {
    width: $menu-item-size;
    height: $menu-item-size;
    margin-right: $padding-x;
    border-radius: 0.4em;
    transition: background-color 150ms ease-out;
    &:focus,
    &:active {
      background-color: rgba(#000000, 0.15);
    }
    &:last-child {
      margin-right: 0;
    }
  }
}

.content {
  width: 100%;
  height: 100%;
  .text {
    padding: $padding-y $padding-x;
  }
}

.form {
  display: flex;
  flex-direction: column;
  .form-item {
    border-bottom: solid $border-size $border-color;
    box-sizing: border-box;
    &.form-content {
      height: 100%;
      textarea {
        height: 100%;
      }
    }
    input[type="text"],
    textarea {
      width: 100%;
      padding: $padding-y $padding-x;
      box-sizing: border-box;
    }
  }
}

.notes {
  overflow: auto;
  .note {
    padding: $padding-y $padding-x;
    border-bottom: solid $border-size $border-color;
    display: flex;
    flex-wrap: nowrap;
    transform: scaleY(1);
    &.note-enter-active,
    &.note-leave-active {
      transition: transform 150ms ease-out;
    }
    &.note-enter,
    &.note-leave-to {
      transform: scaleY(0);
    }
    &:last-child {
      border-bottom: 0;
    }
    .note-link {
      color: inherit;
      width: 100%;
      text-decoration: none;
      overflow: hidden;
      .note-title {
        font-weight: bold;
        margin-bottom: $padding-y;
        white-space: nowrap;
        text-overflow: ellipsis;
        a {
          color: #000000;
        }
      }
      .note-content {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
    }
    .delete-link {
      color: inherit;
      width: 2.25em;
      height: 2.25em;
      display: flex;
      position: relative;
      svg {
        width: 70%;
        height: 70%;
        margin: 0;
        padding: 0;
        vertical-align: top;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translateX(-50%) translateY(-50%);
      }
    }
  }
}

.change-icon-enter-active,
.change-icon-leave-active {
  transition: opacity 150ms ease-out;
}
.change-icon-enter,
.change-icon-leave-to {
  opacity: 0;
}
</style>

