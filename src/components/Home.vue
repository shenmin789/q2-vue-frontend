<template>
    <div class="text-center text-white">
        <b-form inline class="justify-content-center">
            <b-form-group
                label="Amount"
                label-class="text-light"
                label-align="left"
                class="mr-4"
            >
                <b-form-input v-model.number="amount" type="number" trim></b-form-input>
            </b-form-group>
            <b-form-group
                label="From"
                label-class="text-light"
                label-align="left"
                class="mr-4"
            >
                <b-form-select v-model="from" :options="currencyOptions" style="width: 200px"></b-form-select>
            </b-form-group>
            <div class="mr-4 align-self-end">
                <b-button variant="link" style="color: white !important;" @click="onSwap"><b-icon icon="arrow-left-right"></b-icon></b-button>
            </div>
            <b-form-group
                label="To"
                label-class="text-light"
                label-align="left"
                class="mr-4"
            >
                <b-form-select v-model="to" :options="currencyOptions" style="width: 200px"></b-form-select>
            </b-form-group>
            <div class="mr-4 align-self-end">
                <b-button variant="warning" @click="onConvert"><b-icon icon="chevron-right"></b-icon></b-button>
            </div>
        </b-form>
        <div class="align-self-end mt-4" style="font-size:25px;">{{selected.converted_amount}} {{selected.from}}</div>
        <div class="d-flex justify-content-center align-items-end">
            <div class="mr-2" style="font-size:60px;">{{selected.converted_amount}}</div>
            <div class="align-self-end mb-3" style="font-size:25px;">{{selected.to}}</div>
        </div>
        <div class="align-self-end" style="font-size:15px;">{{selected.from_detail}}</div>
        <div class="align-self-end" style="font-size:15px;">{{selected.to_detail}}</div>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    name: 'Home',
    data () {
        return {
            amount: 0,
            from: 'USD',
            to: 'MYR',
            conversion_rates: '',
            currencyOptions: ['EUR', 'USD', 'MYR'],
            selected:{
                amount: '',
                from: '',
                to: '',
                converted_amount: '',
                from_detail: '',
                to_detail: ''
            }
        }
    },
    methods:{
        get_data(){
            axios.get(`http://localhost:8000/currencies`)
            .then(response => {
            // JSON responses are automatically parsed.
                this.conversion_rates = response.data
                // console.log(response);
            })
        },
        clear_selected(){
            this.selected = {
                amount: '',
                from: '',
                to: '',
                converted_amount: '',
                from_detail: '',
                to_detail: ''
            }
        },
        onSwap(){
            [this.from, this.to] = [this.to, this.from]

            
            this.clear_selected()
        },
        onConvert(){
            if(!this.amount){
                alert('please insert your amount')
                return false
            }
            if(this.from == this.to){
                alert('please select another combination')
                return false
            }
            let combination = this.from+"_"+this.to
            let reverse_combination = this.to+"_"+this.from
            const rightToLeft = this.conversion_rates.find(element => element.name == combination);
            const leftToRight = this.conversion_rates.find(element => element.name == reverse_combination);
            this.selected.amount = this.amount
            this.selected.from = this.from
            this.selected.to = this.to
            this.selected.converted_amount = parseFloat(rightToLeft.value)*this.amount
            let f = 1*parseFloat(rightToLeft.value)
            let t = 1*parseFloat(leftToRight.value)
            this.selected.from_detail = "1 "+this.selected.from+" = "+f+" "+this.selected.to
            this.selected.to_detail = "1 "+this.selected.to+" = "+t+" "+this.selected.from
        }
    },
    mounted(){
        this.get_data()
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
