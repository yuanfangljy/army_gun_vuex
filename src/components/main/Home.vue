<template>
  <div class="home">
       <!-- WRAPPER -->
      <div id="wrapper">
            
            <!-- Start LEFT SIDEBAR And Navbar -->
              <app-left-sidebar-navbar></app-left-sidebar-navbar>
            <!-- END   LEFT SIDEBAR And Navbar -->

            <!-- Start MAIN -->
            <div class="main">
              <!-- <app-map></app-map> -->
              <router-view></router-view>
            </div>  
            <!-- END   MAIN -->

            <!-- Start Footer -->
              <app-footer></app-footer>
            <!-- End   Footer -->
      </div>
 </div>
</template>

<script>
import Footer from "../shelf/Footer"
import LeftSidebarNavbar from "../shelf/LeftSidebarNavbar";
//import Navbar from "../shelf/Navbar";

export default {
  name: 'Home',
  components:{
    appFooter:Footer,
    appLeftSidebarNavbar:LeftSidebarNavbar,
    //appNavbar:Navbar,
  }
}
</script>

<style scoped>
    html,body,#home{
            width: 100%;
            height: 100%;
            overflow: hidden;
            margin: 0;
            font-family: "微软雅黑";
        }
</style>
