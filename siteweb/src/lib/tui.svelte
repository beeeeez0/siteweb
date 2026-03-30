<script lang="ts" >
    let propos = $props();
</script>

<div class="terminal-line">
    <span class="user">abdallah@</span>
    <span class="host">bz0.ca ~ </span>
    <span class="dollar"> $</span>
    <span class="command">{propos.text}</span>
</div>

<style>
    .terminal-line {
        font-family: 'Courier New', Courier, monospace;
        font-size: 0;
    }

    .user {
        font-size:medium;
    }

    .host {
        color: #4CAF50;
        font-size:medium;
    }

    .dollar {
        margin-left: 0.1in;
        font-size:medium;
    }

    .command {
        margin-left: 0.1in;
        font-size:medium;
    }

</style>