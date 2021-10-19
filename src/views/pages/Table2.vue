<template>
  <div>
    <vx-card title="테이블 테스트">
      <a-table :columns="columns" :data-source="data">
        <a slot="name" slot-scope="text">{{ text }}</a>
      </a-table>
    </vx-card>
    <vx-card title="데이터 입력" style="margin-top:50px">
      <vs-input class="inputx" placeholder="Name" v-model="name"></vs-input>
      <vs-input class="inputx" placeholder="Age" v-model="age"></vs-input>
      <vs-input class="inputx" placeholder="Address" v-model="address"></vs-input>
      <vs-button
          @click="onAddData()"
          icon-pack="feather"
          icon="icon-chevrons-right"
          icon-after
          class="shadow-md w-full lg:mt-0 mt-4">신규데이터추가</vs-button>
    </vx-card>
  </div>
</template>

<script>
import firebase from "firebase";

export default {
  data() {
    return {
      name: "",
      age: "",
      address: "",
      columns: [
        {
          title: 'Name',
          dataIndex: 'name',
          key: 'name',
          scopedSlots: {customRender: 'name'},
        },
        {
          title: 'Age',
          dataIndex: 'age',
          key: 'age',
          width: 80,
        },
        {
          title: 'Address',
          dataIndex: 'address',
          key: 'address 1',
          ellipsis: true,
        }
      ], data: [
        {
          key: '1',
          name: 'John Brown',
          age: 32,
          address: 'New York No. 1 Lake Park, New York No. 1 Lake Park',
          tags: ['nice', 'developer'],
        },
        {
          key: '2',
          name: 'Jim Green',
          age: 42,
          address: 'London No. 2 Lake Park, London No. 2 Lake Park',
          tags: ['loser'],
        },
        {
          key: '3',
          name: 'Joe Black',
          age: 32,
          address: 'Sidney No. 1 Lake Park, Sidney No. 1 Lake Park',
          tags: ['cool', 'teacher'],
        },
      ]
    }
  },
  methods: {
    onAddData() {
      console.log("aaaaa")
      var db = firebase.firestore();
      var self = this;
      var user = firebase.auth().currentUser;
      if (user) {
        db.collection("bbs")
            .add({
              uid: user.uid,
              name: self.name,
              age: self.age,
              address: self.address,
            }).then(function () {
          alert("적용!")
        })
      }
    }
  }
}
</script>
