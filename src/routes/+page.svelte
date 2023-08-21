<script>
    import Table from "$lib/components/table.svelte";
    import { onMount } from 'svelte';
  
    let candidates = []; // Initialize candidates with data
  
    async function fetchData() {
	  try {
		const apiKey = 'TEST1236C4CF23E6921C41429A6E1D546AC9535E';
		const apiUrl = `https://api.recruitly.io/api/candidate/?apiKey=${apiKey}`;
		const response = await fetch(apiUrl);
		const apiData = await response.json();
		candidates = apiData.data; // Extract the data array from the API response
		console.log(candidates);
	  } catch (error) {
		console.error('Error fetching data:', error);
	  }
	}
  
	fetchData();
  </script>
  
  <Table {candidates} />
  