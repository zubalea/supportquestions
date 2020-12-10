
This is the data.    
Notice the first ID that starts with 020.    
The computedItem does not return that entry.   But if I rename the ID to start with a20 then it works.

The DATA via an api.
[
    {
        "id": "020d540c-8006-40bd-890a-f2a74a3a4d0b",
        "parent": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "tag": "2e7eb990-3b0e-11eb-8728-15519f5e3c10",
        "icon": "",
        "title": "My New Dash",
        "group": null,
        "url": "2e7eb990-3b0e-11eb-8728-15519f5e3c10",
        "type": "L",
        "category": "U",
        "customer": null,
        "user": null,
        "access_groups": [],
        "services": [],
        "sort_order": 10,
        "version": 1607620819553678
    },
    {
        "id": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "parent": null,
        "tag": "dashboard",
        "icon": "mdi-view-dashboard",
        "title": "dashboard",
        "group": "/dashboard",
        "url": null,
        "type": "H",
        "category": "S",
        "customer": null,
        "user": null,
        "access_groups": [],
        "services": [],
        "sort_order": 1,
        "version": 1607617504553902
    },
    {
        "id": "ae3c7aa8-a49e-4bbb-9ca2-b7725d8e8e1a",
        "parent": "1c047169-5eae-400e-ab02-4dd4bd503790",
        "tag": "global",
        "icon": null,
        "title": "dash_global",
        "group": null,
        "url": "global",
        "type": "L",
        "category": "S",
        "customer": null,
        "user": null,
        "access_groups": [],
        "services": [],
        "sort_order": 1,
        "version": 1607620346544090
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
    
    
