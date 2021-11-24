<template>
    <div class="tree-menu">
        <div>{{name}}</div>
        <tree-menu
        v-for="child in children"
        :key="child"
        :children="child.children"
        :name="child.name"
        :depth="depth + 1"
        ></tree-menu>
    </div>
</template>

<script>
export default {
    props: ['name', 'children', 'depth'],
    name: 'tree-menu',
}
</script>