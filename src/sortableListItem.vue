<template>
    <li :id="id" :class="classNames" draggable="true" @dragstart="dragstart" @dragover="dragover" @drop="drop" @click.ctrl="addToSelected" @dragleave="dragleave">
        <span class="above"></span>
        <slot></slot>
    </li>
</template>
<script>
export default {
    props: {
        item: [String, Object],
        isSelected: Boolean,
        idField: String,
        itemClassName: {
            type: String,
            default: "item"
        },
        selectedClassName: {
            type: String,
            default: "selected"
        },
        aboveHoverClassName: {
            type: String,
            default: "over"
        }
    },
    computed: {
        classNames: function () {
            return this.isSelected ? `${this.selectedClassName} ${this.itemClassName}` : this.itemClassName
        },
        id: function () {
            return this.idField ? this.item[this.idField] : this.item
        }
    },
    methods: {
        addToSelected: function () {
            if (this.isSelected) {
                this.$emit("removeSelected", this.id)
            } else {
                this.$emit("addToSelected", this.id)
            }
        },
        dragstart: function (ev) {
            this.$emit("addToSelected", ev.target.id)
        },
        dragover: function (ev) {
            ev.preventDefault();
            if (ev.target.className.indexOf('above') !== -1) {
                ev.target.classList.add(this.aboveHoverClassName);
            }
        },
        dragleave: function (ev) {
            if (ev.target.className.indexOf("above") !== -1) {
                ev.target.classList.remove(this.aboveHoverClassName);
            }
        },
        drop: function (ev) {
            ev.preventDefault();
            let isAbove = ev.target.className.indexOf('above') !== -1
            if (isAbove) {
                ev.target.classList.remove(this.aboveHoverClassName);
            }
            let id = this.findAncestor(ev.target, this.itemClassName).id;
            this.$emit("onDrop", id, isAbove)
        },
        findAncestor(el, cls) {
            while (el.className.indexOf(cls) < 0 && (el = el.parentNode));
            return el;
        }
    }
}
</script>
<style scoped>
.item.selected {
  text-decoration: underline;
}
li .above {
  width: 100%;
  height: 10px;
  display: block;
  z-index: 1;
  position: absolute;
}
li .above.over {
  background: #eee;
}
</style>