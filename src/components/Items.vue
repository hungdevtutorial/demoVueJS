<template>
  <div>
    <AddItems :callBack="callBackEmployees" />
    <el-table
      :data="
        employeesData.filter(
          (data) =>
            !search || data.name.toLowerCase().includes(search.toLowerCase())
        )
      "
      style="width: 100%;"
    >
      <el-table-column label="Date" prop="date"></el-table-column>
      <el-table-column label="Name" prop="name"></el-table-column>
      <el-table-column align="right">
        <template slot="header" :slot-scope="scope">
          <el-input v-model="search" size="mini" placeholder="Type to search" />
        </template>
        <template slot-scope="scope">
          <UpdateItems
            :callBack="callBackEmployees"
            :name="scope.row.name"
            :id="scope.row.id"
            :date="scope.row.date"
          />
          <DeleteItems :callBack="callBackEmployees" :id="scope.row.id" />
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
import firebase from "../configuration/firebaseConfig";
import AddItems from "./AddItems";
import UpdateItems from "./UpdateItems";
import DeleteItems from "./DeleteItems";
const db = firebase.firestore();

export default {
  components: {
    AddItems,
    UpdateItems,
    DeleteItems
  },
  data() {
    return {
      name: "",
      date: new Date().toISOString().slice(0, 10),
      employeesData: [],
      search: ""
    };
  },
  methods: {
    callBackEmployees() {
      this.readEmployees();
    },
    readEmployees() {
      this.employeesData = [];
      db.collection("employees")
        .get()
        .then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.employeesData.push({
              id: doc.id,
              name: doc.data().name,
              date: doc.data().date
            });
            console.log(doc.id, " => ", doc.data());
          });
        })
        .catch(error => {
          console.log("Error getting documents: ", error);
        });
    }
  },
  mounted() {
    this.readEmployees();
  }
};
</script>
