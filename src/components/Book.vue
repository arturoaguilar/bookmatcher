<template>
<p>Title: {{book.volumeInfo.title}}</p>
<p > Image: {{book.volumeInfo.imageLinks.thumbnail}}</p>
<p>Description: {{book.volumeInfo.description}}</p>
</template>
<script>
export default {
    name:'Book',
    props: ['book'],
    setup() {
        
    },
}
</script>