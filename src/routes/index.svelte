<script>
  import { onMount } from "svelte"

  // const gsheetURL = 'https://docs.google.com/spreadsheets/d/1b8A_3nyTz-8K4D9rcl97dS-Umdmqe0a6s0jbYc6Fgos/edit#gid=1801264911'
  // const gsheetCSV = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQVJh8qxOZeCe0XRjDWSy0aoCJUTX_1691UrBl3tAdcBiG5cVZdXldfWKXmJJsdMD9c3egS_3KmZua0/pub?gid=1801264911&single=true&output=csv'
  const gsheetTSV = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRFrSRLPYbnTAKEZN52TfO559EICpNXqdpHvDSB4cFiIAsljCuhmDL8DZqrGl_7z-1ubjo4oYDJMGig/pub?gid=1801264911&single=true&output=tsv'

  let allCourses = []
  $: loading = true
  
  onMount(async function() {
    const response = await fetch(gsheetTSV)
    const rtext = await response.text()
    loading = false;
    const rows = rtext.split("\r\n")
    allCourses = rows.map((r) => {
      let cols = r.split("\t")
      let description = cols[3]
      if (description === "NULL") description = ""
      return {
        code: cols[0],
        title: cols[1],
        credits: cols[2],
        description
      }
    }).filter((c) => c.code !== undefined && c.code !== 'EVENT_ID')
    courses = allCourses
  })
  let filterText = ""
  $: filtering = filterText != ""
  $: courses = allCourses.filter((c) => 
      c.code.toLocaleUpperCase().includes(filterText.toLocaleUpperCase())
    )
</script>

<svelte:head>
	<title>Salem College Courses</title>
</svelte:head>

<div class="wrap" class:loading class:filtering>
  {#if loading}
    <div class="card">
      <svg aria-hidden="true" focusable="false" data-prefix="fas" data-icon="spinner" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="svg-inline--fa fa-spinner fa-pulse fa-w-16 fa-3x"><path fill="currentColor" d="M304 48c0 26.51-21.49 48-48 48s-48-21.49-48-48 21.49-48 48-48 48 21.49 48 48zm-48 368c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zm208-208c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zM96 256c0-26.51-21.49-48-48-48S0 229.49 0 256s21.49 48 48 48 48-21.49 48-48zm12.922 99.078c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.491-48-48-48zm294.156 0c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.49-48-48-48zM108.922 60.922c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.491-48-48-48z"></path></svg>
    </div>
  {:else}
    <header class="filters">
      <div class="container">
        <div></div>
        <h1>Salem College Courses</h1>
        <div></div>
        <label>Code Filter: <input bind:value={filterText} size=4 ></label>
    </div>
    </header>
    <div class="container courses">
      {#if filterText}
        <h3>{filterText.toLocaleUpperCase()} Courses ({filterText.toLocaleUpperCase()})</h3>
      {/if}
      {#each courses as c}
        <h4>{c.code}. {c.title} <em>({c.credits} hrs)</em></h4>
        <p>{c.description || " "}</p>
      {/each}
    </div>
  {/if}
</div>
<style>
  .filters {
    background: darkblue;
    color: white;
    padding: 1em;
    height: 2rem;
  }
  .filtering .filters {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
  }
  input {
    text-transform: uppercase;
  }
  .card {
    width: 10em;
  }
  .loading {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: var(--c-blue);
  }
  svg {
    max-width: 240px;
    color: gold;
    border-radius: 1em;
    display: block;
  }
  .fa-pulse {
    animation: fa-spin 1s steps(8) infinite;
  }
  .wrap {
    min-height: 100vh;
    margin: 0;
  }
  .filtering.wrap::before {
    content: ' ';
    height: 4rem;
    display: block;
  }
  header .container {
    display: flex;
    flex-direction: row-reverse;
    justify-content: space-between;
  }
  h1 {
    margin: 0;
  }
  .container {
    max-width: 100ch;
    margin: auto;
  }
  .courses {
    padding: 0 1em;
  }
  
  @-webkit-keyframes fa-spin{0%{-webkit-transform:rotate(0deg);transform:rotate(0deg)}to{-webkit-transform:rotate(1turn);transform:rotate(1turn)}}
  @keyframes fa-spin{0%{-webkit-transform:rotate(0deg);transform:rotate(0deg)}to{-webkit-transform:rotate(1turn);transform:rotate(1turn)}}
</style>