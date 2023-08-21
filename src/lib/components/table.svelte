<script>
    import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';
    import { Button, Modal } from 'flowbite-svelte';
  
    // Define the selectedCandidate for editing
  export  let selectedCandidate = null;
    export let candidates = [];
  
    // Modal visibility state
    let isModalOpen = false;
  
    // Function to open the modal for editing
    function openEditModal(candidate) {
      selectedCandidate = candidate;
      isModalOpen = true;
    }
  
    function editCandidate(updatedCandidate) {
      // Find the index of the selectedCandidate in the candidates array
      const index = candidates.findIndex(candidate => candidate.id === updatedCandidate.id);
      if (index !== -1) {
        candidates[index] = updatedCandidate;
      }
      isModalOpen = false; // Close the modal after editing
    }
  
 


// Function to open the delete confirmation modal


  
  async function deleteCandidate(candidate) {
    const confirmation = confirm('Are you sure you want to delete this candidate?');
    if (confirmation) {
      candidates = candidates.filter(c => c.id !== candidate.id);

      // Make an API call to update the server
      const response = await fetch(`https://api.recruitly.io/api/candidate/${candidate.id}/?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json'
        },
        // Add any necessary authentication headers or tokens
      });

      if (response.ok) {
        // Candidate deleted successfully on the server
        console.log("Job deleted successfully!");
    
      } else {
        // Handle error if deletion on server failed
        console.error('Failed to delete candidate on the server');
      }
    }
  }
  </script>
  
  <main>
    <Table striped={true}>
        <TableHead class="text-xs text-rose-500 uppercase bg-gray-100 dark:bg-gray-700 dark:text-gray-400">
            <TableHeadCell>ID</TableHeadCell>
          <TableHeadCell>First Name</TableHeadCell>
          <TableHeadCell>Surname</TableHeadCell>
          <TableHeadCell>Mobile</TableHeadCell>
          <TableHeadCell>Email</TableHeadCell>
          <TableHeadCell>Action</TableHeadCell>
        </TableHead>
        <TableBody class="divide-y">
          {#each candidates as candidate}
            <TableBodyRow>
                <TableBodyCell>{candidate.id}</TableBodyCell> 
              <TableBodyCell>{candidate.firstName}</TableBodyCell>
              <TableBodyCell>{candidate.surname}</TableBodyCell>
              <TableBodyCell>{candidate.mobile}</TableBodyCell>
              <TableBodyCell>{candidate.email}</TableBodyCell>
              <TableBodyCell>
                <button on:click={() => openEditModal(candidate)}>Edit</button>
                <button on:click={() => deleteCandidate(candidate)}>Delete</button>
              </TableBodyCell>
            </TableBodyRow>
          {/each}
        </TableBody>
      </Table>
  
    <!-- Modal for editing -->
{#if isModalOpen && selectedCandidate}
<Modal title="Edit Candidate" bind:open={isModalOpen} autoclose={false}>
  <div slot="content">
    <form>
      <label for="firstName">First Name:</label>
      <input type="text" id="firstName" bind:value={selectedCandidate.firstName} />

      <label for="surname">Surname:</label>
      <input type="text" id="surname" bind:value={selectedCandidate.surname} />

      <label for="mobile">Mobile:</label>
      <input type="text" id="mobile" bind:value={selectedCandidate.mobile} />

      <label for="email">Email:</label>
      <input type="email" id="email" bind:value={selectedCandidate.email} />
    </form>
  </div>
  <div slot="footer">
    <Button on:click={() => editCandidate(selectedCandidate)}>Save</Button>
    <Button on:click={() => isModalOpen = false} color="alternative">Cancel</Button>
  </div>
</Modal>
{/if}



  </main>
  
  <style>
    /* Style for the modal */
    .modal {
      /* Add styles for positioning the modal */
    }
  
    .modal-content {
      /* Add styles for the modal content */
    }
  </style>
  