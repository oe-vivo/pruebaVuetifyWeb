<template>
  
        
  
  
  <v-app>
    <v-navigation-drawer>
      <SideBar /> 
    </v-navigation-drawer>
    <v-main>
      <default-view />

    </v-main>
    
  </v-app>

</template>

<script setup>
    import SideBar from '@/components/sidebar.vue' 

    import DefaultBar from './AppBar.vue'
    import DefaultView from './View.vue'
</script>
