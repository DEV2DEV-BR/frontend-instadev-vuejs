<template>
  <q-page class="flex column">
    <MenuDrawer
      :drawerRight="drawerRight"
      @close="drawerRight = false"
    />
    <div
      class="column q-pt-xl q-pb-md q-px-md full-width bg-grey-2"
      :class="drawerRight && 'drawer-open'"
    >
      <q-icon
        name="las la-bars"
        size="31px"
        class="absolute-right q-ma-lg cursor-pointer"
        @click="drawerRight = !drawerRight"
      />
      <div class="full-width row items-center justify-center">
        <q-icon name="fas fa-lock" color="black" size="11px" class="q-mr-xs" />
        <strong>{{user.user_name}}</strong>
        <q-icon name="fas fa-chevron-down" color="black" size="11px" class="q-ml-xs" />
      </div>
      <div class="row items-center justify-between full-width q-mt-lg">
        <q-avatar size="96px" class="avatar-profile">
          <img class="avatar" :src="user.avatar || 'https://static.wikia.nocookie.net/caramella-girls/images/9/99/Blankpfp.png'">
        </q-avatar>
        <div class="column items-center">
          <strong>{{posts.length}}</strong>
          <span>Posts</span>
        </div>
        <div class="column items-center">
          <strong>834</strong>
          <span>Followers</span>
        </div>
        <div class="column items-center">
          <strong>162</strong>
          <span>Following</span>
        </div>
      </div>
      <div class="column q-mt-md">
        <strong>{{user.name}}</strong>
        <span v-if="user.bio">
          {{user.bio}}
        </span>
        <span v-else>
          Adicione uma descrição a sua bio.
        </span>
        <span>
          Everything is designed.
        </span>
      </div>
      <q-btn
        color="white"
        flat
        dense
        class="btn-edit full-width q-my-md"
        text-color="black"
        label="Edit Profile"
        @click="goTo('profile')"
      />
      <q-btn
        color="white"
        flat
        dense
        class="btn-edit full-width q-mb-lg"
        text-color="black"
        label="Delete Profile"
        @click="deleteUser()"
      />
      <div class="row">
        <div class="column items-center q-mr-md">
          <q-avatar size="64px" class="avatar-story">
            <q-icon name="las la-plus" size=""></q-icon>
          </q-avatar>
          <span>New</span>
        </div>
        <div class="column items-center q-mr-md" v-for="item in 3" :key="item">
          <q-avatar size="64px" class="avatar-story">
            <img class="avatar" src="https://cdn.quasar.dev/img/avatar.png">
          </q-avatar>
          <span>Friends</span>
        </div>
      </div>
    </div>
    <div
      class="full-width"
      :class="drawerRight && 'drawer-open'"
    >
      <q-separator/>
      <q-tabs
        v-model="tab"
        class="text-teal"
        active-color="grey-8"
      >
        <q-tab name="grid">
            <q-img src="../../../src/assets/grid.svg" width="22px"/>
        </q-tab>
        <q-tab name="profile">
            <q-img src="../../../src/assets/profile.svg" width="22px"/>
        </q-tab>
      </q-tabs>
      <div class="row q-mb-xl">
        <q-img
          v-for="item in posts" :key="item.id"
          class="cursor-pointer col-4"
          :ratio="1"
          :src="item.image"
        />
      </div>
    </div>
    <div
      class="full-width q-pb-lg"
      :class="drawerRight ? 'bottom-menu-drawer-open' : 'container-bottom'"
    >
      <q-separator class="full-width q-mb-sm"/>
      <BottomBar/>
    </div>
  </q-page>
</template>

<script>
import BottomBar from 'src/components/BottomBar/Index';
import MenuDrawer from 'src/components/MenuDrawer/Index';

export default {
  name: 'MyArea',
  data() {
    return {
      tab: 'grid',
      drawerRight: false,
      user: {},
      token: this.$store.getters['auth/getJWT'],
      posts: [],
    };
  },
  async mounted() {
    await this.loadAllPosts();
    this.loadProfileData();
  },
  methods: {
    loadProfileData() {
      this.user = this.$store.getters['user/getUserData'];
    },
    async loadAllPosts() {
      const { data } = await
      this.$store.dispatch('posts/listMyPosts', { token: this.token });
      this.posts = data;
    },
    goTo(route) {
      this.$router.push({ path: route });
    },
    deleteUser() {
      this.$q.dialog({
        title: 'Excluir conta de usuário',
        message: 'Essa é uma exclusão irreversível!',
        cancel: true,
      }).onOk(async () => {
        this.$store.dispatch('user/deleteUserProfile', { token: this.token });
        this.$router.push({ path: 'sign-in' });
      }).onCancel(() => {
        // console.log('>>>> Cancel')
      });
    },
  },
  components: {
    BottomBar,
    MenuDrawer,
  },
};
</script>

<style lang="scss" scoped>
.q-avatar {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border: solid 2px #C7C7CC;
  padding: 4px;
}
.avatar-profile {
  .avatar {
    width: 90px;
    height: 88px;
  }
}

.avatar-story {
  .avatar {
    width: 60px;
    height: 55px;
  }
}

.btn-edit {
  height: 35px;
  border-radius: 6px;
  text-transform: none;
  border: 1px solid rgba(60, 60, 67, 0.18);
}
.drawer-open {
  position: relative;
  right: 70%;
}

.bottom-menu-drawer-open {
  position: absolute;
  right: 70%;
  bottom: 0px
}

.menu-drawer {
  width: 70%;
}
</style>
