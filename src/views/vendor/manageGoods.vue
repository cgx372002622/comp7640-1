<script setup>
import maGoods from '@/components/maGoods.vue'
import {ref,onMounted} from "vue";
import {getOneVendorProduct,updateGoodsInfo,delOneProduct} from "@/api/vendor/request.js";
import {paginationByEl} from "@/util/myselfFun.js";
import {useVendorStore} from "@/stores/index.js";
import router from "@/router/index.js";
const vendorStore = useVendorStore()
const allData = ref([])
const page_size = ref(4)
const total = ref(0)
const current = ref(1)

onMounted( async () => {
  await paginationByEl(getOneVendorProduct,page_size.value,1,{vendorId:vendorStore.vendorId},total,allData)
})

const handleChange = async (value) => {
  await paginationByEl(getOneVendorProduct,page_size.value,value,{vendorId:vendorStore.vendorId},total,allData)
}

const changeName = ({productId,value}) => {
  allData.value.forEach(item => {
    if (item.productId === productId){
      item.productName = value
    }
  })
}

const changePrice = ({productId,value}) => {
  allData.value.forEach(item => {
    if (item.productId === productId){
      item.listedPrice = value
    }
  })
}

const changeInventory = ({productId,value}) => {
  allData.value.forEach(item => {
    if (item.productId === productId){
      item.inventory = value
    }
  })
}

const changeTag = ({productId,value}) => {
  allData.value.forEach(item => {
    if (item.productId === productId){
      item.tags = value.join(',')
    }
  })
}

const remove =  async (id) => {
  await delOneProduct(id)
  current.value = 1
  await paginationByEl(getOneVendorProduct,page_size.value,1,{vendorId:vendorStore.vendorId},total,allData)
  router.go(0)
}

const submitChange = async () => {
  const res = await updateGoodsInfo(allData.value)
  await paginationByEl(getOneVendorProduct,page_size.value,1,{vendorId:vendorStore.vendorId},total,allData)
}

</script>

<template>
  <ma-goods v-for="item in allData" :key="item.productId"
            :imgUrl="item.imgUrl"
            :inventory="item.inventory"
            :listedPrice="item.listedPrice"
            :productId="item.productId"
            :tags="item.tags"
            :productName="item.productName"
            @modifyName="changeName"
            @modifyPrice="changePrice"
            @modifyInventory="changeInventory"
            @modifyTag="changeTag"
  >
  <template #button="obj">
    <el-button type="danger" @click="remove(obj.productId)">Remove</el-button>
  </template>
  </ma-goods>
  <el-pagination v-if="allData.length !== 0" background layout="prev, pager, next" :total="total"
                 :page-size="page_size" @current-change="handleChange"
  />
  <div class="submit">
    <el-button type="primary" round @click="submitChange">Change</el-button>
  </div>

</template>

<style scoped>
.submit{
  display: flex;
  justify-content: end;
}
</style>
