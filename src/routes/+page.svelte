<script lang="ts">
    import '../global.css';
    import { onMount } from 'svelte';

    // set subCnt to 0 or the value in localStorage
    let subCnt:number = getSubCnt();

    function getSubCnt():number {
        if(typeof localStorage !== 'undefined') {
            return parseInt(localStorage.getItem('subCnt') || '0');
        }
        return 0;
    }

    const updateSubCnt = (newValue:number) => {
        if(typeof localStorage !== 'undefined') {
            localStorage.setItem('subCnt', newValue.toString());
        }
        subCnt = newValue;
    }   

    const handleIncrementClick = () => {
        updateSubCnt(subCnt+1);
    }

    const handleDecrementClick = () => {
        if(subCnt==0) 
            return;
        updateSubCnt(subCnt-1);
    }

    onMount(() => {
        // check for old form data
        if(typeof localStorage !== 'undefined') {
            const oldFormData = localStorage.getItem('formData');
        }   
    });
</script>

<h1>Test</h1>

<div class="incr-btn-container">
    <button class="btn" on:click={handleDecrementClick}> - </button>
    <p>Subject Count: {subCnt}</p>
    <button class="btn" on:click={handleIncrementClick}> + </button>

    {#if subCnt>0}
        <div class="form">

        </div>
    {/if}
</div>

