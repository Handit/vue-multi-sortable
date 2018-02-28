<template>
    <ul :class="listClassName">
        <sortable-list-item v-for="(item,index) in items" 
        :item="item" 
        :key="index" 
        :is-selected="isSelected(item)" 
        :id-field="idField"
        :item-class-name="itemClassName"
        :selected-class-name="selectedClassName"
        @addToSelected="addToSelected"
        @onDrop="onDrop"
        @removeSelected="removeSelected"
        :above-hover-class-name="aboveHoverClassName">
            <slot :item="item"></slot>
        </sortable-list-item>
    </ul>
</template>
<script>
import sortableListItem from "./sortableListItem.vue"
export default {
    components: { sortableListItem },
    props: {
        items: { type: Array, default: [] },
        idField: String,
        itemClassName: String,
        selectedClassName: String,
        listClassName: String,
        aboveHoverClassName: String
    },
    data: function () {
        return { selected: [] }
    },
    methods: {
        isSelected: function (item) {
            if (this.idField) {
                return this.selected.indexOf(item[this.idField]) !== -1
            }
            return this.selected.indexOf(item) !== -1
        },
        getIndex: function (itemId) {
            if (this.idField) {
                return this.items.findIndex(item => item[this.idField] == itemId)
            }
            return this.items.indexOf(itemId)
        },
        cloneItems: function () {
            if (this.idField) {
                return this.items.map(item => item[this.idField])
            }
            return this.items.slice(0)
        },
        addToSelected: function (itemId) {
            if (this.selected.indexOf(itemId) === -1) {
                this.selected.push(itemId)
                this.selected.sort((a, b) => this.getIndex(a) < this.getIndex(b))
            }
        },
        removeSelected: function (itemId) {
            const index = this.selected.indexOf(itemId);
            if (index !== -1) {
                this.selected.splice(index, 1)
            }
        },
        onKeyUp: function (e) {
            if (e.keyCode === 27) {
                this.selected = []
            }
        },
        onDrop: function (targetId, isAbove) {
            if (this.selected.indexOf(targetId) === -1) {
                const moved = this.selected
                this.$emit("change", moved, targetId, isAbove);
            }
            this.selected = []
        }
    },
    mounted: function () {
        document.addEventListener("keyup", this.onKeyUp)
    },
    destroy: function () {
        document.removeEventListener("keyup", this.onKeyUp)
    }
}
</script>