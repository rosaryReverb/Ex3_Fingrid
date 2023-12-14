<script>
    import { onMount } from 'svelte';
  
    let api_key = import.meta.env.VITE_API_KEY;
    const variable_id = 245; // Tuulivoimatuotannon ennusteiden VariableId
  
    // Haetaan data 1.8.2023 päivältä
    const start_time = '2023-08-01T00:00:00Z';
    const end_time = '2023-08-02T00:00:00Z';
  
    let data = [];
  
    async function fetchData() {
      try {
        const url = `https://api.fingrid.fi/v1/variable/${variable_id}/events/json?start_time=${encodeURIComponent(start_time)}&end_time=${encodeURIComponent(end_time)}`;
        const response = await fetch(url, {
          headers: {
            'x-api-key': api_key,
            'Accept': 'application/json'
          }
        });
  
        if (!response.ok) {
          throw new Error(`Virhe: ${response.status}`);
        }
  
        const jsonData = await response.json();
        data = jsonData;
      } catch (error) {
        console.error('Virhe datan haussa:', error);
      }
    }
  
    onMount(() => {
      fetchData();
    });
  </script>
  
  <main>
    <h1>Fingridin Tuulivoimatuotannon Ennusteet 1.8.2023</h1>
    {#if data.length > 0}
      <table>
        <thead>
          <tr>
            <th>Alkamisaika</th>
            <th>Päättymisaika</th>
            <th>Arvo</th>
          </tr>
        </thead>
        <tbody>
          {#each data as item}
            <tr>
              <td>{item.start_time}</td>
              <td>{item.end_time}</td>
              <td>{item.value}</td>
            </tr>
          {/each}
        </tbody>
      </table>
    {:else}
      <p>Ladataan dataa...</p>
    {/if}
  </main>
  