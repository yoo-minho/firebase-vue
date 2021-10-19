<template>
  <div>
    <vx-card title="테이블 테스트">
      <a-table :columns="columns" :data-source="data">
        <template slot="operation" slot-scope="record">
          <vs-button @click="() => edit(record)">선택</vs-button>
        </template>
        <a slot="name" slot-scope="text">{{ text }}</a>
      </a-table>
    </vx-card>
    <vx-card title="데이터 입력" style="margin-top:50px">
      <vs-input class="inputx" placeholder="Name" v-model="name"></vs-input>
      <vs-input class="inputx" placeholder="Age" v-model="age"></vs-input>
      <vs-input class="inputx" placeholder="Address" v-model="address"></vs-input>
      <vs-button @click="onAddData()" size="small">신규데이터추가</vs-button>
      <vs-button @click="onUpdateData()" size="small">업데이트</vs-button>
      <vs-button @click="onDeleteData()" size="small">삭제</vs-button>
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
      key: "",
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
        },
        {
          title: 'action',
          key: 'address 1',
          scopedSlots: {customRender: "operation"}
        }
      ],
      data: []
    }
  },
  mounted() {
    this.onLoadData();
  },
  methods: {
    edit(r) {
      console.log(r)
      this.name = r.name;
      this.age = r.age;
      this.address = r.address;
      this.key = r.key;
    },
    onInitData() {
      this.key = "";
      this.name = "";
      this.age = "";
      this.address = "";
    },
    onRefreshData() {
      var self = this;
      this.data = this.data.filter(item => {
        if (item.key === self.key) {
          item.name = self.name;
          item.age = self.age;
          item.address = self.address;
        }
        return item;
      })
      this.onInitData();
    },
    onPopupData() {
      var self = this;
      this.data = this.data.filter(item => (item.key !== self.key))
      this.onInitData();
    },
    onLoadData() {
      var db = firebase.firestore();
      var self = this;
      self.data = [];
      db.collection("bbs").get().then(function (querySnapshot) {
        querySnapshot.forEach(function (doc) {
          var _t = doc.data();
          _t["key"] = doc.id
          self.data.push(_t)
        })
      })
    },
    onUpdateData() {
      var db = firebase.firestore();
      var _ref = db.collection("bbs").doc(this.key);
      var self = this;
      var updateJson = {
        name: self.name,
        age: self.age,
        address: self.address
      }
      _ref.update(updateJson).then(() => {
        self.onRefreshData();
        console.log("update!!")
      }).catch(error => {
        console.error("error", error);
      })
    },
    onDeleteData() {
      var self = this;
      var db = firebase.firestore();
      db.collection("bbs").doc(this.key).delete().then(() => {
        self.onPopupData();
        console.log("delete");
      }).catch((error) => {
        console.error("error", error);
      })
    },
    onAddData() {
      console.log("aaaaa")
      var db = firebase.firestore();
      var self = this;
      var user = firebase.auth().currentUser;
      if (user) {
        var addData = {
          uid: user.uid,
          name: self.name,
          age: self.age,
          address: self.address,
        }
        db.collection("bbs").add(addData).then(function (mRef) {
          addData.key = mRef.id;
          self.data.push(addData);
        })
      }
    }
  }
}
</script>
