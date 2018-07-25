<template>
  <div id="app">
    <LeftNav></LeftNav>
    <div class="main">
      <router-view/>
    </div>
  </div>
</template>

<script>
  import LeftNav from '@/components/common/leftNav'
export default {
  name: 'App',
  components:{
    LeftNav
  }
}
</script>

<style>
.main {
  float:left;
  width:95%;
  background: #eff2f7;
  height:100%;
}
</style>
