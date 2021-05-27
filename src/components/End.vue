<template>
    <div> <p>Thank you for playing {{name}}. </p><p> Your points are: {{points}}</p></div>
</template>
<script>
export default {
    name: "End",
    props:['name','points'],
    setup(props) {
        console.log("TEST PROPS STATE");
        console.log(props.playerState);
    },
}
</script>