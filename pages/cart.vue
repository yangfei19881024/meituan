<template>
    <div class="page-cart">
        <el-row>
            <el-col 
                v-if="cart.length" 
                :span='24'
                class="m-cart">
                <list :cart-date="cart"/>
                <p>
                    应付金额:<em class="money">¥{{total}}</em>
                </p>
                <div class="post">
                    <el-button type="primary" @click="submit">提交定订单</el-button>
                </div>
            </el-col>
            <el-col 
                v-else
                class="empty"><p>购物车为空<p/>
                <img src="https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=857635139,1779129256&fm=26&gp=0.jpg" alt="">
                </el-col>
        </el-row>
    </div>
</template>
<script>
import List from '@/components/cart/list'
    export default{ 
        layout:'default_b',
        data(){
            return{
                cart:[]
            }
        },
        components:{
            List
        },
        computed:{
            total(){
                let total=0
                this.cart.forEach(item=>{
                    total+=item.price*item.count
                })
                return total
            }
        },
        methods:{
            submit:async function () {
                let {status,data:{code,id}}=await this.$axios.post('http://127.0.0.1:3000/order/createOrder',{
                    count:this.cart[0].count,
                    price:this.cart[0].price,
                    id:this.cartNo
                })
                if(status==200&&code===0){
                    this.$alert(`恭喜您,已成功下单,订单号${id}`,'下单成功',{
                        confirmButtonText:'确定',
                        callback:action=>{
                            location.href='/order'
                        }
                    })
                }
            }
        },
        async asyncData(ctx){
            let {status,data:{code,data:{name,price}}}=await ctx.$axios.post('http://127.0.0.1:3000/cart/getCart',{
                id:ctx.query.id
            })
            if(status===200&&code===0&&name){
                return {
                    cart:[{
                        name,
                        price,
                        count:1
                    }],
                    cartNo:ctx.query.idd
                }
            }
        }
        
    }
</script>
<style lang='scss'>
    @import '@/assets/css/cart/index.scss'

</style>
