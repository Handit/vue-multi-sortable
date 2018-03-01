## Vue-multi-sortable

A simple Vue.js component to handle sortable/draggable lists.

[Live demo here](https://vue-sortable.herokuapp.com/) and [demo code here](https://github.com/Handit/vue-multi-sortable-example)

------

Vue-multi-sortable is simple to use, just install the package:

```
npm install vue-multi-sortable --save
```

And import in your component:

```javascript
import sortable from "vue-multi-sortable"
...

export default {
	components : {sortable},
 	...
}
```

In template put the sortable component

```vue
<template>
	<div id="myApp">
        <span> Look at my sortable list below </span>
        <sortable :items="items" @change="onChange">
            <template slot-scope="{item}">
                {{item.label}}
            </template>
        </sortable>
    </div>
</template>
```

Now you must provide the `items` property and the `change` callback:

```javascript
export default {
    components: { sortable },
    data: function () {
        return {
            items: [{ id: 1, label: "Real Madrid" }, ... ]
        }
    },
    methods: {
        onChange: function (moved, targetId, isAbove) {
            targetId = parseInt(targetId);
            if (isAbove) {
                moved = moved.reverse()
            }
            moved.forEach(itemId => {
                itemId = parseInt(itemId);
                let foundItem = this.items.find(item => item.id === itemId)
                if (foundItem) {
                    let index = this.items.indexOf(foundItem);
                    this.items.splice(index, 1)
                    let targetIdIndex = this.items.findIndex(item => item.id === targetId);
                    this.items.splice(targetIdIndex + (isAbove ? 0 : 1), 0, foundItem)
                }
            })
        }
    }
}
```

// TODO:

- [ ] Increment docs
- [ ] Implement tests