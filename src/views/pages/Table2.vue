<template>
  <div>
    <vx-card title="테이블 테스트">
      <a-table :columns="columns" :data-source="data">
        <template slot="img" slot-scope="record">
          <img v-if="record.img" :src="record.img" alt="record.alt" style="width:50px; height:50px;">
        </template>
        <template slot="operation" slot-scope="record">
          <vs-button @click="() => edit(record)">선택</vs-button>
        </template>
        <a slot="name" slot-scope="text">{{ text }}</a>
      </a-table>
    </vx-card>
    <vx-card title="데이터 입력" style="margin-top:50px">
      <div style="display:flex; margin-bottom: 10px">
        <vs-input class="inputx" placeholder="Name" v-model="name" style="margin-left:10px"></vs-input>
        <vs-input class="inputx" placeholder="Age" v-model="age" style="margin-left:10px"></vs-input>
        <vs-input class="inputx" placeholder="Address" v-model="address" style="margin-left:10px"></vs-input>
      </div>
      <vs-button @click="onAddData()" size="small" style="margin-left:10px">신규데이터추가</vs-button>
      <vs-button @click="onUpdateData()" size="small" style="margin-left:10px">업데이트</vs-button>
      <vs-button @click="onDeleteData()" size="small" style="margin-left:10px">삭제</vs-button>

      <div>이미지 업로드</div>

      <div class="col-md-10" style="margin-bottom: 10px" v-if="key">
        <a-upload
            :fileList="fileList"
            :beforeUpload="beforeUpload"
            @change="handleChange">
          <a-button><a-icon type="upload"></a-icon> 업로드 </a-button>
        </a-upload>
        <img v-if="img" :src="img" alt="alt" style="width: 50px; height: 50px;"/>
      </div>
    </vx-card>
  </div>
</template>

<script>
import firebase from "firebase";

export default {
  data() {
    return {
      fileList: "",
      name: "",
      img: "",
      alt: "",
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
          title: 'img',
          key: 'img',
          scopedSlots: {customRender: "img"}
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
    beforeUpload(file) {
      this.fileList = [...this.fileList, file];
      return false
    },
    handleChange(info) {
      if (info.file.status === "removed") {
        this.handleRemove();
      } else {
        this.createFile(info.file, 0);
      }
    },
    handleRemove() {
      this.fileList = [];
      this.img = "";
    },
    createFile(file, idx) {
      if (!file.type.match("image.*")) {
        alert("not image!");
        this.handleRemove();
        return;
      }
      var reader = new FileReader();
      var vm = this;
      reader.onload = function (e) {
        vm.saveToFirebaseStorage(e, file, idx);
      };
      reader.readAsDataURL(file)
    },
    saveToFirebaseStorage(evt, items, idx) {
      var _key = new Date().getTime() + idx;
      var self = this;
      var storageRef = firebase.storage().ref();
      var _name = items.name.replace(/[~`!#$%^&*+=\-\[\]\\';,/{}()|":<>?]/g, "");
      var user = firebase.auth().currentUser;
      var uploadTask = storageRef.child("data/" + user.uid + "/" + _key + "/" + _name).put(items);
      uploadTask.on("state_changed", function (snapshot) {
            var progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
            console.log("Upload is " + progress + "% done");
            switch (snapshot.state) {
              case firebase.storage.TaskState.PAUSED: // or 'paused'
                console.log("Upload is paused");
                break;
              case firebase.storage.TaskState.RUNNING: // or 'running' console.log("Upload is running");
                break;
            }
          },
          function (error) {
            console.log(error);
          },
          function () {
            uploadTask.snapshot.ref.getDownloadURL().then(function (downloadURL) {
              self.img = downloadURL;
            })
          });
    },
    edit(r) {
      this.name = r.name;
      this.age = r.age;
      this.address = r.address;
      this.key = r.key;
      this.img = r.img;
    },
    onInitData() {
      this.key = "";
      this.name = "";
      this.age = "";
      this.address = "";
      this.img = "";
    },
    onRefreshData() {
      var self = this;
      this.data = this.data.filter(item => {
        if (item.key === self.key) {
          item.name = self.name;
          item.age = self.age;
          item.address = self.address;
          item.img = self.img;
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
        address: self.address,
        img: self.img
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
      if(this.key) {
        this.onInitData();
        alert('불가');
        return;
      }
      var db = firebase.firestore();
      var self = this;
      var user = firebase.auth().currentUser;
      if (user) {
        var addData = {
          uid: user.uid,
          name: self.name,
          age: self.age,
          address: self.address,
          img: self.img
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
