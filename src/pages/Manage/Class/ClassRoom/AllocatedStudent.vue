<template>
  <!-- Main Content START -->
  <div class="main-content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12 col-sm-6 col-md-6">
          <RecordsComponent :updatePaginationParent="updatePagination" />
        </div>
        <div class="col-12 col-sm-6 col-md-4 offset-md-2">
          <SearchContentComponent :searchFilterParent="searchFilter" />
        </div>
      </div>
      <!-- LIST VIEW -->
      <div class="row">
        <div class="col-12">
          <el-table ref="singleTable" stripe :data="posts" highlight-current-row style="width: 100%">
            <el-table-column sortable property="sn" label="SN" width="80"></el-table-column>
            <el-table-column sortable property="name" label="Name"></el-table-column>
            <el-table-column sortable property="surname" label="Surname"></el-table-column>
            <el-table-column sortable property="usi" label="USI"></el-table-column>
            <el-table-column sortable property="grade" label="Grade"></el-table-column>
            <el-table-column sortable property="room" label="Room"></el-table-column>
            <el-table-column sortable property="weekDays" label="Week Days">
              <template v-slot="scope">
                <button v-for="(post, idx) in scope.row.weekDays" :key="idx" class="tags button medium ed-btn__secondary">
                  <span>{{post}}</span>
                </button>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <el-pagination 
          background 
          layout="prev, pager, next" 
          @current-change="handleCurrentChange" 
          :current-page.sync="currentPage"
          :page-size="pageSize" 
          :total="totalSize">
          </el-pagination>
        </div>
      </div>
      <div v-if="busy" class="preloader">
        <span><img src="../../../../assets/images/preloader.gif" /> Loading...</span>
      </div>
    </div>
  </div>
</template>

<script>
  // COMPONENTS
  import RecordsComponent from '../../../../components/records/RecordsComponent.vue';
  import SearchContentComponent from '../../../../components/search/SearchContentComponent.vue';


  export default {
    name: "allocated-student",
    components: {
      RecordsComponent,
      SearchContentComponent
    },
    // DATA
    data: () => ({
      posts: [],
      page: 1,
      pageSize: 10,
      totalSize: 0,
      currentPage:1,
      searchName: "",
      loadedData:[],
      busy: false
    }),
    methods: {
      loadMore() {
        this.busy = true;

        this.axios.get("https://raw.githubusercontent.com/nmihin/ed-intelligence-admin/main/public/list-class-student.json").then((response) => {
            this.totalSize = response.data.length;
            this.loadedData = response.data;

            const append = response.data.slice(
              this.posts.length,
              this.posts.length + this.pageSize
            );

            this.posts = append;

            this.busy = false;
        }).catch((error) => error.response.data)
      },
      updatePagination(value) {
        this.pageSize = value;
        this.currentPage = 1;

        const allocatedStudentStorage = this.loadedData;

        this.posts = [];
        const append = allocatedStudentStorage.slice(
          this.posts.length,
          this.posts.length + this.pageSize
        );

        this.posts = append;
      },
      searchFilter(value) {
        this.busy = true;

        const allocatedStudentStorage = this.loadedData;

        this.posts = allocatedStudentStorage.filter((data) =>
          data.name.toLowerCase().includes(value.toLowerCase()) ||
          data.surname.toLowerCase().includes(value.toLowerCase()) ||
          data.usi.toString().toLowerCase().includes(value.toLowerCase()) ||
          data.grade.toLowerCase().includes(value.toLowerCase()) ||
          data.room.toLowerCase().includes(value.toLowerCase())
        );

        this.totalSize = allocatedStudentStorage.length;

        this.busy = false;
        return allocatedStudentStorage;
      },
      handleCurrentChange(val) {
        this.busy = true;
        const allocatedStudentStorage = this.loadedData;
        this.page = val;

        // CHECK IF SEARCH EMPTY
        if (this.searchName === "") {
          this.totalSize = allocatedStudentStorage.length;

          const append = allocatedStudentStorage.slice(
            (this.page - 1) * this.pageSize,
            (this.page - 1) * this.pageSize + this.pageSize
          );

          this.posts = append;
        } else {

          this.posts = allocatedStudentStorage.filter((data) =>
            data.name.toLowerCase().includes(this.searchName.toLowerCase())
          );

          this.totalSize = this.posts.length;

          const append = this.posts.slice(
            (this.page - 1) * this.pageSize,
            (this.page - 1) * this.pageSize + this.pageSize
          );

          this.posts = append;
        }

        this.busy = false;
      }
    },
    created() {
      this.loadMore();
    }
  }

</script>
