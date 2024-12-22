<script setup lang="ts">
import {type Reactive, reactive, ref} from "vue";

  type Node = {
    id: string;
    value: string;
    name: string;
    show: boolean;
    children?: Node[];
  }

  const id = ref<string>("");
  const newName = ref<string>("");
  const newValue = ref<string>("");
  const show = ref<boolean>(true);

  const matchTree = ref<Node | null>();

  const tree = reactive<Node>({
    id: crypto.randomUUID(),
    name: "A",
    value: "A-value",
    show: true,
    children: [
      {
        id: crypto.randomUUID(),
        name: "A-a",
        value: "A-a-value",
        show: true,
      },
      {
        id: crypto.randomUUID(),
        name: "A-b",
        value: "A-b-value",
        show: true,
        children: [
          {
            id: crypto.randomUUID(),
            name: "A-b-a",
            value: "A-b-a-value",
            show: true,
          },
          {
            id: crypto.randomUUID(),
            name: "A-b-b",
            value: "A-b-b-value",
            show: true,
          }
        ]
      }
    ],
  });


  const findNode = (parent: Reactive<Node>, id: string): Promise<Node | null> => {
    let find = false;
    let over = false;

    return new Promise((resolve) => {
      const match = (p: Node) => {
        if(!find) {
          if(p.id === id) {
            find = true;
            resolve(p);
          } else if(p.children && p.children.length > 0) {
            over = false;
            p.children.forEach((v) => {
              match(v)
            })
          } else {
            over = true;
          }
        }
      }

      match(parent)

      if(over) {
        resolve(null);
      }
    })
  }

  const findOneNode = (id: string) => {
    show.value = true;
    findNode(tree, id).then(node => {
      matchTree.value = node;

      if(node) {
        newName.value = node.name;
        newValue.value = node.value;
      }
    })
  }

  const setName = () => {
    if(matchTree.value) {
      matchTree.value.name = newName.value;
    }
  }

  const setValue = () => {
    if(matchTree.value) {
      matchTree.value.value = newValue.value;
    }
  }

  const setHide = () => {
    show.value = !show.value;

    if(matchTree.value) {
      matchTree.value.show = show.value;
    }
  }
</script>

<template>
  <div style="display: flex; gap: 6px;">
    <div style="width: 50%;">
      <pre>{{tree}}}</pre>
    </div>
    <div style="width: 50%;">
      <div>
        <input v-model="id" /> <button @click="() => findOneNode(id)">get</button>
      </div>

      <div>
        <input v-model="newName" /> <button @click="setName">set name</button>
      </div>

      <div>
        <input v-model="newValue" /> <button @click="setValue">set value</button>
      </div>

      <div>
        <button @click="setHide">toggle {{show}}</button>

      </div>

      <div>
        <pre>{{matchTree}}</pre>
      </div>
    </div>
  </div>
</template>