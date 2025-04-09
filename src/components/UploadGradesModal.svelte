<script>
  import { createEventDispatcher } from 'svelte';
  import { jwt } from '../stores/auth';
  
  const dispatch = createEventDispatcher();
  
  export let course;
  
  let file = null;
  let error = '';
  let success = '';
  let isSubmitting = false;
  
  function handleFileChange(e) {
    file = e.target.files[0];
  }
  
  async function handleSubmit() {
    if (!file) {
      error = 'Please select a CSV file';
      return;
    }
    
    isSubmitting = true;
    error = '';
    success = '';
    
    try {
      const formData = new FormData();
      formData.append('file', file);
      formData.append('course_id', course.id);
      
      const response = await fetch('http://localhost:5000/upload-grades', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${$jwt}`
        },
        body: formData
      });
      
      if (response.ok) {
        success = 'Grades uploaded successfully!';
        setTimeout(() => dispatch('close'), 1500);
      } else {
        error = await response.text();
      }
    } catch (err) {
      error = 'Failed to upload grades';
    } finally {
      isSubmitting = false;
    }
  }
</script>

<div class="modal show d-block" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-lg" role="document">
    <div class="modal-content">
      <div class="modal-header bg-info text-white">
        <h5 class="modal-title">Upload Grades for {course.name}</h5>
        <button type="button" class="btn-close btn-close-white" on:click={() => dispatch('close')}></button>
      </div>
      <div class="modal-body">
        {#if error}
          <div class="alert alert-danger">{error}</div>
        {/if}
        
        {#if success}
          <div class="alert alert-success">{success}</div>
        {:else}
          <div class="mb-4">
            <p>Upload a CSV file with student grades. The file should contain the following columns:</p>
            <ul>
              <li>Student ID</li>
              <li>Grade (A, B, C, etc.)</li>
              <li>Comments (optional)</li>
            </ul>
          </div>
          
          <div class="mb-3">
            <label for="csvFile" class="form-label">CSV File</label>
            <input 
              class="form-control" 
              type="file" 
              id="csvFile" 
              accept=".csv" 
              on:change={handleFileChange}
            >
          </div>
          
          <div class="alert alert-light">
            <small>Note: The system will update existing grades or add new ones based on the Student ID.</small>
          </div>
        {/if}
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" on:click={() => dispatch('close')}>Cancel</button>
        {#if !success}
          <button type="button" class="btn btn-info" on:click={handleSubmit} disabled={isSubmitting}>
            {#if isSubmitting}
              <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
              Uploading...
            {:else}
              Upload Grades
            {/if}
          </button>
        {/if}
      </div>
    </div>
  </div>
</div>