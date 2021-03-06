<template>
  <base-container>
    <h2>Active Users</h2>
    <base-search
      @search="updateSearch"
      :search-term="enteredSearchTerm"
    ></base-search>
    <div>
      <button @click="sort('asc')" :class="{ selected: sorting === 'asc' }">
        Sort Ascending
      </button>
      <button @click="sort('desc')" :class="{ selected: sorting === 'desc' }">
        Sort Descending
      </button>
    </div>
    <ul>
      <user-item
        v-for="user in displayedUsers"
        :key="user.id"
        :user-name="user.fullName"
        :id="user.id"
        @list-projects="$emit('list-projects', $event)"
      ></user-item>
    </ul>
  </base-container>
</template>

<script>
import { ref, watch, computed } from 'vue';
import UserItem from './UserItem.vue';

export default {
  components: {
    UserItem,
  },
  emits: ["list-projects"],
  props: ['users'],
  setup(props) {
    // Data
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');
    const sorting = ref(null);

    // Methods
    function updateSearch(val) {
      enteredSearchTerm.value = val;
    }
    function sort(mode) {
      sorting.value = mode;
    }

    // Watchers
    watch(enteredSearchTerm, (val) => {
      setTimeout(() => {
        if (val === enteredSearchTerm.value) {
          activeSearchTerm.value = val;
        }
      }, 300);
    });

    // Computed Properties
    const availableUsers = computed(() => {
      let users = [];
      if (activeSearchTerm.value) {
        users = props.users.filter((usr) =>
          usr.fullName.toLowerCase().includes(activeSearchTerm.value.toLowerCase())
        );
      } else if (props.users) {
        users = props.users;
      }
      return users;
    });
    const displayedUsers = computed(() => {
      if (!sorting.value) {
        return availableUsers.value;
      }
      return availableUsers.value.slice().sort((u1, u2) => {
        if (sorting.value === 'asc' && u1.fullName > u2.fullName) {
          return 1;
        } else if (sorting.value === 'asc') {
          return -1;
        } else if (sorting.value === 'desc' && u1.fullName > u2.fullName) {
          return -1;
        } else {
          return 1;
        }
      });
    })


    return {
      enteredSearchTerm,
      activeSearchTerm,
      sorting,
      updateSearch,
      sort,
      availableUsers,
      displayedUsers
    };
  },
  computed: {
    // displayedUsers() {
    //   if (!this.sorting) {
    //     return this.availableUsers;
    //   }
    //   return this.availableUsers.slice().sort((u1, u2) => {
    //     if (this.sorting === 'asc' && u1.fullName > u2.fullName) {
    //       return 1;
    //     } else if (this.sorting === 'asc') {
    //       return -1;
    //     } else if (this.sorting === 'desc' && u1.fullName > u2.fullName) {
    //       return -1;
    //     } else {
    //       return 1;
    //     }
    //   });
    // },
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>