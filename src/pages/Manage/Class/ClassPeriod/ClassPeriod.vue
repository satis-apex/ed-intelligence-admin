<template>
  <!-- Main Content START -->
  <div class="main-content">
    <!-- DELETE CODE START -->
    <DeleteClassPeriodModal 
        ref="DeleteClassPeriodModal"
        :deleteClassPeriodConfirmParent="deleteCodeConfirm"
    />
    <!-- DELETE CODE END -->
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
            <el-table-column sortable property="grade" label="Grade"></el-table-column>
            <el-table-column sortable property="subjectName" label="Subject Name"></el-table-column>
            <el-table-column sortable property="teacher" label="Teacher"></el-table-column>
            <el-table-column sortable property="type" label="Type"></el-table-column>
            <el-table-column sortable property="action" width="260" label="Action">
              <template v-slot="scope">
                <div @click="assignSelectedAction(scope.row.sn);" class="element">
                  <el-tooltip class="item" effect="dark" content="Assign student" placement="top">
                    <i class="icon icon-student"></i>
                  </el-tooltip>
                </div>
                <div @click="relocateSelectedAction(scope.row.sn);" class="element">
                  <el-tooltip class="item" effect="dark" content="Relocate Student" placement="top">
                    <i class="icon icon-entry"></i>
                  </el-tooltip>
                </div>
                <div @click="viewSelectedAction(scope.row.sn);" class="element">
                  <el-tooltip class="item" effect="dark" content="View Allocated Student" placement="top">
                    <i class="icon icon-eye"></i>
                  </el-tooltip>
                </div>
                <div @click="deleteSelectedAction(scope.row.sn);" class="element">
                  <el-tooltip class="item" effect="dark" content="Delete" placement="top">
                    <i class="icon icon-delete"></i>
                  </el-tooltip>
                </div>
                <div @click="schedulingSelectedAction(scope.row.sn);" class="element">
                  <el-tooltip class="item" effect="dark" content="Unit Scheduling" placement="top">
                    <i class="icon icon-menu-list"></i>
                  </el-tooltip>
                </div>
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
  import DeleteClassPeriodModal from '../ClassPeriod/modals/DeleteClassPeriodModal.vue';

  export default {
    name: "class-period",
    components: {
      RecordsComponent,
      SearchContentComponent,
      DeleteClassPeriodModal
    },
    // DATA
    data: () => ({
      posts: [],
      page: 1,
      currentPage:1,
      pageSize: 10,
      totalSize: 0,
      searchName: "",
      loadedData:[],
      busy: false
    }),
    methods: {
      deleteSelectedAction(sn) {
        this.$refs.DeleteClassPeriodModal.openModal(sn);
        //this.codeSelectedToDelete = sn;
      },
      deleteCodeConfirm(code) {
        const classPeriodStorage = this.loadedData;
        const codeSN = code;

        // FILTER MENU LIST
        const codeDeleted = classPeriodStorage.filter(function(item) {
          return item.sn !== codeSN;
        });

        this.posts = [];
        const append = codeDeleted.slice(
          this.posts.length,
          this.posts.length + this.pageSize
        );

        this.posts = append;
      },
      assignSelectedAction(sn) {
        this.$router.push({
          path: '/class-period/assign-student/' + sn
        })
      },
      relocateSelectedAction(sn) {
        this.$router.push({
          path: '/class-period/release-student/' + sn
        })
      },
      viewSelectedAction(sn) {
        this.$router.push({
          path: '/class-period/allocated-student/' + sn
        })
      },
      schedulingSelectedAction(sn) {
        this.$router.push({
          path: '/class-period/unit-schedule/' + sn,
          query: { subject: 'English', grade: 'PK4', student: 'Alena Sipes Herzog' }
        })
      },
      loadMore() {
        this.busy = true;

        this.axios.get("https://raw.githubusercontent.com/nmihin/ed-intelligence-admin/main/public/class-period.json").then((response) => {
            this.totalSize = response.data.length;
            this.loadedData = response.data;

            this.posts = [];
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

        const classPeriodStorage = this.loadedData;

        this.posts = [];
        const append = classPeriodStorage.slice(
          this.posts.length,
          this.posts.length + this.pageSize
        );

        this.posts = append;
      },
      searchFilter(value) {
        this.busy = true;

        const classPeriodStorage = this.loadedData;

        this.posts = classPeriodStorage.filter((data) =>
          data.grade.toLowerCase().includes(value.toLowerCase()) || 
          data.subjectName.toLowerCase().includes(value.toLowerCase()) ||
          data.teacher.toLowerCase().includes(value.toLowerCase()) ||  
          data.type.toLowerCase().includes(value.toLowerCase())
        );

        this.totalSize = classPeriodStorage.length;

        this.busy = false;
        return classPeriodStorage;
      },
      handleCurrentChange(val) {
        this.busy = true;
        const classPeriodStorage = this.loadedData;
        this.page = val;

        // CHECK IF SEARCH EMPTY
        if (this.searchName === "") {
          this.totalSize = classPeriodStorage.length;

          const append = classPeriodStorage.slice(
            (this.page - 1) * this.pageSize,
            (this.page - 1) * this.pageSize + this.pageSize
          );

          this.posts = append;
        } else {

          this.posts = classPeriodStorage.filter((data) =>
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
