<script>
  import { createEventDispatcher } from 'svelte';
  import { jwt } from '../stores/auth';
  
  const dispatch = createEventDispatcher();
  
  export let course;
  
  let student = {
    student_id: '',
    name: '',
    email: '',
    enrollment_date: '',
    course_id: course.id
  };
  
  let error = '';
  let isSubmitting = false;
  
  async function handleSubmit() {
    if (!student.student_id || !student.name || !student.email || !student.enrollment_date) {
      error = 'All fields are required';
      return;
    }
    
    // Simple email validation
    if (!student.email.includes('@')) {
      error = 'Please enter a valid email address';
      return;
    }
    
    isSubmitting = true;
    error = '';
    
    try {
      const response = await fetch('http://localhost:5000/student', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${$jwt}`
        },
        body: JSON.stringify(student)
      });
      
      if (response.ok) {
        dispatch('close');
      } else {
        error = await response.text();
      }
    } catch (err) {
      error = 'Failed to register student';
    } finally {
      isSubmitting = false;
    }
  }
</script>

<div class="modal show d-block" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-lg" role="document">
    <div class="modal-content">
      <div class="modal-header bg-success text-white">
        <h5 class="modal-title">Register Student for {course.name}</h5>
        <button type="button" class="btn-close btn-close-white" on:click={() => dispatch('close')}></button>
      </div>
      <div class="modal-body">
        {#if error}
          <div class="alert alert-danger">{error}</div>
        {/if}
        
        <form on:submit|preventDefault={handleSubmit}>
          <div class="mb-3">
            <label for="studentId" class="form-label">Student ID</label>
            <input type="text" class="form-control" id="studentId" bind:value={student.student_id}>
          </div>
          <div class="mb-3">
            <label for="studentName" class="form-label">Student Name</label>
            <input type="text" class="form-control" id="studentName" bind:value={student.name}>
          </div>
          <div class="mb-3">
            <label for="studentEmail" class="form-label">Email Address</label>
            <input type="email" class="form-control" id="studentEmail" bind:value={student.email}>
          </div>
          <div class="mb-3">
            <label for="enrollmentDate" class="form-label">Enrollment Date</label>
            <input type="date" class="form-control" id="enrollmentDate" bind:value={student.enrollment_date}>
          </div>
        </form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" on:click={() => dispatch('close')}>Cancel</button>
        <button type="button" class="btn btn-success" on:click={handleSubmit} disabled={isSubmitting}>
          {#if isSubmitting}
            <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
            Registering...
          {:else}
            Register Student
          {/if}
        </button>
      </div>
    </div>
  </div>
</div>