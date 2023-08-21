<script>
  import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';
  import { Button, Modal } from 'flowbite-svelte';
  import { Input, Label, Helper, Checkbox, A } from 'flowbite-svelte';
 

  // Define the selectedCandidate for editing
export  let selectedCandidate = null;
  export let candidates = [];

  // Modal visibility state
  let isModalOpen = false;
 
let isPopupOpen = false;


  function openEditPopup(candidate) {
  selectedCandidate = candidate;
  isPopupOpen = true;
}async function editCandidate(updatedCandidate) {
// Find the index of the selectedCandidate in the candidates array
const index = candidates.findIndex(candidate => candidate.id === updatedCandidate.id);
if (index !== -1) {
  candidates[index] = updatedCandidate;

  // Make an API call to update the server
  const response = await fetch(`https://api.recruitly.io/api/candidate?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E`, {
    method: 'POST', // Use PUT method to update existing data
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(updatedCandidate) // Send the updated candidate data
    // Add any necessary authentication headers or tokens
  });

  if (response.ok) {
    // Candidate updated successfully on the server
    console.log("Candidate updated successfully!");
  } else {
    // Handle error if update on server failed
    console.error('Failed to update candidate on the server');
  }
}
isPopupOpen = false; // Close the popup after editing
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
  alert("deleted successfully!");
  
    } else {
      // Handle error if deletion on server failed
      console.error('Failed to delete candidate on the server');
    }
  }
}

export  let isAddModalOpen = false;
let newCandidate = {
firstName: '',
surname: '',
mobile: '',
email: ''
};

async function addCandidate() {
// Make an API call to add the new candidate
const response = await fetch(`https://api.recruitly.io/api/candidate?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(newCandidate)
  // Add any necessary authentication headers or tokens
});

if (response.ok) {
  // Candidate added successfully on the server
  console.log("Candidate added successfully!");
  candidates.push(newCandidate); // Add the new candidate to the candidates array
  isAddModalOpen = false; // Close the modal after adding
} else {
  // Handle error if addition on server failed
  console.error('Failed to add candidate on the server');
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
        <Button color="yellow" on:click={() => isAddModalOpen = true}>Add</Button>

        
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
              <Button color="blue" on:click={() => openEditPopup(candidate)}>Edit</Button >

              <Button  color="blue" on:click={() => deleteCandidate(candidate)}>Delete</Button >
            </TableBodyCell>
          </TableBodyRow>
        {/each}
      </TableBody>
    </Table>


    {#if selectedCandidate} <!-- Check if selectedCandidate is not null -->
    <div class="popup-background" style="display: {isPopupOpen ? 'block' : 'none'}">
      <div class="popup-content">
         
<form>
  <div class="grid gap-6 mb-6 md:grid-cols-1">
    <div>
      <Label for="first_name" class="mb-2">First name</Label>
      <Input type="text" id="first_name" bind:value={selectedCandidate.firstName}/>
    </div>
    <div>
      <Label for="last_name" class="mb-2">Surnname</Label>
      <Input type="text" id="last_name"  bind:value={selectedCandidate.surname}/>
    </div>
    <div>
      <Label for="phone" class="mb-2">Mobile</Label>
    <Input type="tel" id="phone" bind:value={selectedCandidate.mobile} />
    </div>
    <div>
      <Label for="email" class="mb-2">Email</Label>
      <Input type="email" id="email" bind:value={selectedCandidate.email}/>
    </div>
  
  </form>
          <div class="popup-buttons">
            <Button on:click={() => editCandidate(selectedCandidate)}>Save</Button>
            <Button on:click={() => isPopupOpen = false} color="alternative">Cancel</Button>
          </div>
        </div>
        
    </div>
  {/if}



  {#if isAddModalOpen}
<div class="popup-background">
<div class="popup-content" style="width: 400px; height: 400px; margin-left: 100px; margin-top:-100px" >
  <form>
  
    <Label for="new_first_name" class="mb-2">First name</Label>
    <Input type="text" id="new_first_name" bind:value={newCandidate.firstName}/>
    <div>
      <Label for="last_name" class="mb-2">Surnname</Label>
      <Input type="text" id="last_name"  bind:value={newCandidate.surname}/>
    </div>
    <div>
      <Label for="phone" class="mb-2">Mobile</Label>
    <Input type="tel" id="phone" bind:value={newCandidate.mobile} />
    </div>
    <div>
      <Label for="email" class="mb-2">Email</Label>
      <Input type="email" id="email" bind:value={newCandidate.email}/>
    </div>
    
    <div class="popup-buttons">
      <Button on:click={addCandidate}>Add</Button>
      <Button on:click={() => isAddModalOpen = false} color="alternative">Cancel</Button>
    </div>
  </form>
</div>
</div>
{/if}

</main>

<style>
 
  .popup-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  width: 400px;
  height: 500px;
  text-align: center; /* Center the content horizontally */
  margin-left:550px;
  margin-top: 100px;
}

.popup-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}
</style>
