<script lang="ts">
  import "../global.css";
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  type formDataType = {
    subjectName: string;
    grade: number;
    credits: number;
  }[];

  // set subCnt or SGPA to 0 or the value in localStorage
  let subCnt: number = getLocalStorageSubCnt();
  let SGPA: number = getLocalStorageSGPA();
  const formData = writable([
    {
      subjectName: "",
      grade: 0,
      credits: 0,
    },
  ]);

  function getLocalStorageSubCnt(): number {
    if (typeof localStorage !== "undefined") {
      return parseInt(localStorage.getItem("subCnt") || "0");
    }
    return 0;
  }

  function getLocalStorageSGPA(): number {
    if (typeof localStorage !== "undefined") {
      return parseInt(localStorage.getItem("SGPA") || "0");
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
    formData.update((value) => [
      ...value,
      { subjectName: "", grade: 0, credits: 0 },
    ]);
    updateSubCnt(subCnt + 1);
  };

  const handleDecrementClick = () => {
    if (subCnt == 0) return;
    formData.update((value) => value.slice(0, value.length - 1));
    updateSubCnt(subCnt - 1);
  };

  const handleSubmit = (e:Event) => {
    // set formData to localStorage
    if (typeof localStorage !== "undefined") {
      localStorage.setItem("formData", JSON.stringify($formData));
    }

    // update SGPA
    SGPA = calculateSGPA($formData);

    // set SGPA to localStorage
    if (typeof localStorage !== "undefined") {
      localStorage.setItem("SGPA", SGPA.toString());
    }
  };

  const calculateSGPA = (formData: formDataType):number => {
    let totalCredits = 0;
    let totalGradePoints = 0;
    formData.forEach((data) => {
      const { grade, credits } = data;
      totalCredits += credits;
      totalGradePoints += grade * credits;
    });

    const SGPA = totalGradePoints / totalCredits;
    return SGPA;
  };

  onMount(() => {
    // set formData to the old value from localStorage
    if(typeof localStorage !== 'undefined') {
        const oldFormData = JSON.parse(localStorage.getItem('formData') || '[]') as formDataType;
        formData.set(oldFormData);
        updateSubCnt(oldFormData.length);
    }
  });
</script>

<h1>SGPA Calculator</h1>

<div class="main-cnt-container">
  <p>Subject Count:</p>

  <div class="incr-btn-container">
    <button class="btn" on:click={handleDecrementClick}> - </button>
    <span>{subCnt}</span>
    <button class="btn" on:click={handleIncrementClick}> + </button>
  </div>
</div>

{#if subCnt > 0}
  <form on:submit|preventDefault={handleSubmit} class="form">
    {#each $formData as data}
      <div class="form-fields-container">
        <div class="form-field">
          <label for="credit">Subject Name:</label>
          <input
            class="subject-name"
            type="text"
            bind:value={data.subjectName}
          />
        </div>

        <div class="form-field">
          <label for="grade">Grade:</label>
          <input
            class="grade"
            name="grade"
            type="number"
            placeholder="Grade"
            min="0"
            bind:value={data.grade}
          />
        </div>

        <div class="form-field">
          <label for="credit">Credit:</label>
          <input
            class="credit"
            name="credit"
            type="number"
            placeholder="Credits"
            min="0"
            bind:value={data.credits}
          />
        </div>
      </div>

      <div class="separator"> </div>
    {/each}
    <button type="submit" class="btn calculate-btn">Calculate</button>
  </form>

  <div class="sgpa-container">
    <p>SGPA: {SGPA}</p>
  </div>
{/if}

<style>
  .main-cnt-container {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .incr-btn-container {
    display: flex;
    gap: 1rem;
  }

  .btn {
    height: 25px;
    padding: 0 1em;
    background-color: black;
    color: white;
    cursor: pointer;
    border: none;
    font-weight: 900 !important;
  }

  .calculate-btn {
    font-weight: 400 !important;
    margin-top: 1em;
    width: 30%;
    font-family:Arial, Helvetica, sans-serif;
  }

  .form {
    margin-top: 20em;
    gap: 1em;
    display: flex;
    flex-direction: column;
    margin-top: 1em;
  }

  .form-fields-container {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .subject-name,
  .grade,
  .credit {
    border: 1px solid black;
  }

  .form-field {
    display:flex;
    justify-content: space-between;
  }

  .form-field > input {
    width:60%;
  }
  .separator {
    height: 1px;
    background-color: black;
  }
</style>
