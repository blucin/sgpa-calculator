<script lang="ts">
  import "../global.css";
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  type formDataType = {
    subjectName: string;
    grade: number;
    credits: number;
  }[];

  // set subCnt to 0 or the value in localStorage
  let subCnt: number = getSubCnt();
  const formData = writable([
    {
      subjectName: "",
      grade: 0,
      credits: 0,
    },
  ]);

  function getSubCnt(): number {
    if (typeof localStorage !== "undefined") {
      return parseInt(localStorage.getItem("subCnt") || "0");
    }
    return 0;
  }

  const updateSubCnt = (newValue: number) => {
    if (typeof localStorage !== "undefined") {
      localStorage.setItem("subCnt", newValue.toString());
    }
    subCnt = newValue;
  };

  const handleIncrementClick = () => {
    formData.update((value) => [...value, { subjectName: "", grade: 0, credits: 0 }]);
    updateSubCnt(subCnt + 1);
  };

  const handleDecrementClick = () => {
    if (subCnt == 0) return;
    formData.update((value) => value.slice(0, value.length - 1));
    updateSubCnt(subCnt - 1);
  };

  /*
    onMount(() => {
        // set formData to the old value in localStorage
        if(typeof localStorage !== 'undefined') {
            const oldFormData = JSON.parse(localStorage.getItem('formData') || '[]') as formDataType;
            formData = oldFormData;
            updateSubCnt(oldFormData.length);
        }
    });
    */
</script>

<h1>SGPA Calculator</h1>

<div class="incr-btn-container">
  <button class="btn" on:click={handleDecrementClick}> - </button>
  <p>Subject Count: {subCnt}</p>
  <button class="btn" on:click={handleIncrementClick}> + </button>

  {#if subCnt > 0}
    <div class="form">
      <ul>
        {#each $formData as data}
          <input
            type="text"
            placeholder="Subject Name"
            bind:value={data.subjectName}
          />
          <input
            type="number"
            placeholder="Grade"
            min="0"
            bind:value={data.grade}
          />
          <input
            type="number"
            placeholder="Credits"
            min="0"
            bind:value={data.credits}
          />
        {/each}
      </ul>
    </div>
  {/if}
</div>