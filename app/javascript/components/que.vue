<template>
  <el-table
    :data="tableData"
    border
    :show-header="false"
    style="width: 100%">
    <el-table-column>
      <template slot-scope="scope">
        <el-table
          :data="scope.row.frankfurts"
          :show-header=false
          style="width: 100%">
          <el-table-column align="center">
            <template slot-scope="scope">
              <el-tag v-if="scope.row.ketchup" type="danger">ketchup</el-tag>
              <el-tag v-if="scope.row.mustard" type="warning">mustard</el-tag>
              <el-tag v-if="!(scope.row.ketchup || scope.row.mustard)" type="info">none</el-tag>
            </template>
          </el-table-column>
        </el-table>
      </template>
    </el-table-column>
    <el-table-column
      label="Time"
      width="180">
      <template slot-scope="scope">
        <el-icon name="time"></el-icon>
        <span style="margin-left: 10px"> {{ scope.row.created_at }}
        </span>
      </template>      
    </el-table-column>
    <el-table-column width="100">
      <template slot-scope="scope">
        <el-button
          size="small"
          type="success"
          @click="done(scope.$index, scope.row)">Done</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
export default {
  data() {
    return {
      tableData: []
    }
  },
  methods: {
    done(index, row) {
      this.tableData.splice(index, 1)
      this.axios.post('http://localhost:5000/customers/' + row.id + '/done', {
      }).then((response) => {
        console.log(response.data)
      })
    },
    addData: function(data) {
      this.tableData.push(data['customer'])
    }
  },
  mounted: function() {
    this.axios.get('http://localhost:5000/customers').then((response) => {
      this.tableData = response.data
    })
  },
  created: function() {
    var that = this
    this.orderChannel = this.$cable.subscriptions.create(
      { channel: 'OrderChannel' },
      {
        received (data) { 
          that.addData(data)
        }
      }
    )
  }
}
</script>

<style scoped>
p {
  font-size: 2em;
  text-align: center;
}
</style>
