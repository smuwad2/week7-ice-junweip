<script>
    export default { 
        // Add code in appropriate places
        data() {
            return {
                items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter'],
                newItem: ""
            }
            
        },
        methods: {
            addItem(){
                this.items.push(this.newItem)
                this.newItem = ''
            }
        }
    }
</script>

<template>

    <h2>Shopping Cart</h2>
    <!-- TODO: Add Code Here -->
    <ul>
        <li v-for="(item, ind) in items">
            {{ item }}
            <button @click="items.splice(ind,1)">Delete!</button>
        </li>
    </ul>
    <input type="text" v-model="newItem" />
    <button @click="addItem">Add</button>
   
</template>
