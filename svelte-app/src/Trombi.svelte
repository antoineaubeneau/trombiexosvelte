<script>
  import persons from "./data.js";

  let selected;
  let filterName = "";
  let filterAge = 100;
  let statusFilter = null;

  $: console.log(selected);

  let selectedPerson = null;

  function handleClick(person) {
    selectedPerson = person;
  }

  function handleNameFilterInput(event) {
    filterName = event.target.value.toLowerCase();
  }

  function handleAgeFilterInput(event) {
    filterAge = event.target.value;
  }

  function handleStatusFilterInput(event) {
    const status = event.target.value;
    if (status === "all") {
      statusFilter = null;
    } else if (status === "alive") {
      statusFilter = (person) => !person.dead;
    }
  }
</script>

<div class="filters">
  <label for="name-filter">Filtrer par nom : </label>
  <input type="text" id="name-filter" on:input={handleNameFilterInput} />

  <label for="age-filter">Filtrer par âge : </label>
  <input
    type="range"
    id="age-filter"
    min="0"
    max="100"
    value="100"
    on:input={handleAgeFilterInput}
  />
  <p>Âge maximal : {filterAge}</p>

  <label>
    <input
      type="radio"
      name="status-filter"
      value="all"
      on:change={handleStatusFilterInput}
      checked
    />
    Tous
  </label>
  <label>
    <input
      type="radio"
      name="status-filter"
      value="alive"
      on:change={handleStatusFilterInput}
    />
    Vivants
  </label>
</div>

<div class="person-list">
  {#each persons as person}
    {#if person.name
      .toLowerCase()
      .includes(filterName) && Math.floor((new Date() - new Date(person.born)) / (365 * 24 * 60 * 60 * 1000)) <= filterAge && (!statusFilter || statusFilter(person))}
      <div class="card">
        <h3>
          <a on:click={() => handleClick(person)}
            >{person.name} {person.surname}</a
          >
        </h3>
        {#if person.dead}
          <p>
            Âge : {Math.floor(
              (new Date(person.dead) - new Date(person.born)) /
                (365 * 24 * 60 * 60 * 1000)
            )} ans (décédé)
          </p>
        {:else}
          <p>
            Âge : {Math.floor(
              (new Date() - new Date(person.born)) / (365 * 24 * 60 * 60 * 1000)
            )} ans
          </p>
        {/if}
      </div>
    {/if}
  {/each}
</div>

<div class="person-details">
  {#if selectedPerson}
    <h3>{selectedPerson.name} {selectedPerson.surname}</h3>
    <p>Date de naissance : {selectedPerson.born}</p>
    <p>Description : {selectedPerson.description}</p>
  {:else}
    <p>Cliquez sur un nom pour afficher les détails de la personne</p>
  {/if}
</div>

<style>
  .person-list {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    margin-top: 2rem;
  }

  .card {
    background-color: white;
    padding: 1rem;
    border-radius: 8px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.2);
    width: calc(33.33% - 1rem);
  }

  .card h3 {
    margin-top: 0;
  }

  .card p {
    margin-bottom: 0;
  }

  .person-details {
    margin-top: 2rem;
    padding: 1rem;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.2);
  }

  .person-details h3 {
    margin-top: 0;
  }

  .filters {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 1rem;
    margin-top: 2rem;
  }

  .filters label {
    font-weight: bold;
  }
</style>
