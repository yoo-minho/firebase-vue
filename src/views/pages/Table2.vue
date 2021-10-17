<template>
  <a-table :columns="columns" :data-source="data">
    <a slot="name" slot-scope="text">{{ text }}</a>
  </a-table>
</template>

<script>
// {
//   'id': 1,
//     'name': 'Leanne Graham',
//     'username': 'Bret',
//     'email': 'Sincere@april.biz',
//     'website': 'hildegard.org'
// }
export default {
  data() {
    return {
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
  }
}
</script>
