
This is the data.    
Notice the first ID that starts with 020.    
The computedItem does not return that entry.   But if I rename the ID to start with a20 then it works.

The DATA via an api.
[
    {
        "id": "020d540c-8006-40bd-890a-f2a74a3a4d0b",
        "parent": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "title": "My New Dash",
    },
    {
        "id": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "parent": null,
        "title": "dashboard",
    },
    {
        "id": "ae3c7aa8-a49e-4bbb-9ca2-b7725d8e8e1a",
        "parent": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "title": "dash_global",
    }
]

Here are the functions used to generate the output.   

    computedItems() {
      // return this.sidebar.map(this.mapItem)
      return this.transformToTree(this.sidebar.map(this.mapItem))
    },
    mapItem(item) {
      return {
        ...item,
        children: item.children ? item.children.map(this.mapItem) : undefined,
        title: this.$t(item.title),
      }
    },
    transformToTree(arr) {
      const nodes = {}

      return arr.filter(function(obj) {
        // debugger
        const id = obj.id
        const parent = obj.parent

        nodes[id] = Object.assign(obj, nodes[id], { children: [] })
        parent &&
          (nodes[parent] = nodes[parent] || { children: [] }).children.push(obj)

        return !parent
      })
    },
    
    
