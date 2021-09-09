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
  let filterText = "";
  $: courses = allCourses.filter((c) => 
      c.code.toLocaleLowerCase().includes(filterText.toLocaleLowerCase())
    )
</script>

<div class="wrap">
{#if loading}
<div class="loading">
  <div>Loading...</div>
  <img alt="Loading" src="Spinner-3.gif">
</div>
{:else}
<div class="filters">
  <label>Code Filter: <input bind:value={filterText} ></label>
</div>
{#if filterText}
<h3>{filterText.toLocaleUpperCase()} Courses</h3>
{/if}
{#each courses as c}
<h4>{c.code} {c.title} <em>({c.credits} hrs)</em></h4>
<p>{c.description}</p>
{/each}
{/if}
</div>
<style>
  .wrap {
    min-height: 100vh;
    margin: auto;
    position: relative;
    max-width: 100ch;
  }
  .filters {
    background: darkblue;
    color: white;
    padding: 1em;
    margin: -0.5em -1em 1.5em;
  }
  .loading {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding-top: 5em;
  }
</style>