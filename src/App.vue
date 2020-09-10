<template>
  <div id="app">
    <modal
      :visible="modalS"
      @set-visible="modalS = $event"
      title="Информация о пользователе">
      <div class="user-info" v-if="selUser">
        <ul class="user-info__list">
          <li>Имя Фамилия: {{ selUser.fullname }}</li>
          <li>Имя пользователя {{ selUser.uname }}</li>
          <li>Компания: {{ selUser.company }}</li>
          <li>Адрес пользователя: {{ selUser.address.streetAddress }}</li>
          <li>Город: {{ selUser.address.city }}</li>
          <li>Штат: {{ selUser.address.state }}</li>
          <li>ZIP: {{ selUser.address.zip }}</li>
        </ul>
      </div>
    </modal>
    <div class="container mt-1">
      <div class="welcome-block" v-if="userList.length === 0">
        <h4 class="welcome-block__title title">
          Пользователи еще не загружены
        </h4>
        <button
          class="btn btn__primary"
          @click="dlUserList()"
          :disabled="loading"
        >
          <span
            v-text="!loading ? 'Загрузить пользователей' : 'Загрузка'">
          </span>
        </button>
        <span class="error" v-if="errorReq">
          Ошибка загрузки, пожалуйста повторите попытку
        </span>
      </div>
      <div class="table-user-list" v-else>
        <table-us-list
          :cols="columns"
          :rows="userList"
          @click-row="setSelUser($event)">
        </table-us-list>
      </div>
    </div>
  </div>
</template>

<script>
import TableUsList from '@/components/Table.vue';
import Modal from '@/components/Modal.vue';

function sendRequest(method, url) {
  // console.log('Отправляю запрос через Url');
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();

    xhr.open(method, url);

    xhr.responseType = 'json';
    xhr.setRequestHeader('Content-Type', 'application/json');

    xhr.onload = () => {
      if (xhr.status >= 400) {
        reject(xhr.response);
      } else {
        resolve(xhr.response);
      }
    };

    xhr.send();
  });
}

export default {
  name: 'App',
  components: {
    TableUsList,
    Modal,
  },
  data() {
    const columns = [
      {
        property: 'fullname',
        title: 'Имя и фамилия',
        direction: null,
        sort: false,
      },
      {
        property: 'uname',
        title: 'Имя пользователя',
        direction: null,
        sort: false,
      },
      {
        property: 'company',
        title: 'Компания',
        direction: null,
        sort: false,
      },
      {
        property: 'state',
        title: 'Штат',
        direction: null,
        sort: false,
      },
    ];
    return {
      columns,
      errorReq: '',
      modalS: false,
      loading: false,
      userList: [],
      selUser: undefined,
    };
  },
  methods: {
    dlUserList() {
      this.loading = true;
      const reqUrl = 'http://www.filltext.com/?rows=1000&id={index}&fullname={firstName}~{lastName}&company={business}&email={email}&uname={username}&address={addressObject}';
      sendRequest('GET', reqUrl)
        .then((resp) => {
          this.errorReq = '';
          this.userList = resp.map((el) => Object.assign(
            el, { state: el.address.state },
          ));
          this.loading = false;
        })
        .catch(() => {
          this.errorReq = 'Ошибка загрузки. Попробуйте снова';
          this.loading = false;
        });
    },
    setSelUser(user) {
      this.selUser = user;
      this.modalS = true;
    },
  },
  created() {
    document.title = 'Тестовое задание Wuzzup';
  },
};
</script>

<style lang="scss">
@import "./styles/main.scss";
.table{
  &__body tr:hover {
    cursor: pointer;
    background: $blue-grey-lighten-4;
  }
}
</style>
