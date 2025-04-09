<script>
  import { createEventDispatcher } from 'svelte';
  import { jwt } from '../stores/auth';
  
  const dispatch = createEventDispatcher();
  
  let course = {
    course_id: '',
    name: '',
    instructor: '',
    semester: ''
  };
  
  let error = '';
  let isSubmitting = false;
  
  async function handleSubmit() {
    if (!course.course_id || !course.name || !course.instructor || !course.semester) {
      error = 'All fields are required';
      return;
    }
    
    isSubmitting = true;
    error = '';
    
    try {
      const response = await fetch('http://localhost:5000/course', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${$jwt}`
        },
        body: JSON.stringify(course)
      });
      
      if (response.ok) {
        dispatch('courseAdded');
        dispatch('close');
      } else {
        error = await response.text();
      }
    } catch (err) {
      error = 'Failed to add course';
    } finally {
      isSubmitting = false;
    }
  }
</script>

<div class="modal show d-block" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-lg" role="document">
    <div class="modal-content">
      <div class="modal-header bg-primary text-white">
        <h5 class="modal-title">Add New Course</h5>
        <button type="button" class="btn-close btn-close-white" on:click={() => dispatch('close')}></button>
      </div>
      <div class="modal-body">
        {#if error}
          <div class="alert alert-danger">{error}</div>
        {/if}
        
        <form on:submit|preventDefault={handleSubmit}>
          <div class="mb-3">
            <label for="courseId" class="form-label">Course ID</label>
            <input type="text" class="form-control" id="courseId" bind:value={course.course_id}>
          </div>
          <div class="mb-3">
            <label for="courseName" class="form-label">Course Name</label>
            <input type="text" class="form-control" id="courseName" bind:value={course.name}>
          </div>
          <div class="mb-3">
            <label for="instructor" class="form-label">Instructor</label>
            <input type="text" class="form-control" id="instructor" bind:value={course.instructor}>
          </div>
          <div class="mb-3">
            <label for="semester" class="form-label">Semester</label>
            <input type="text" class="form-control" id="semester" bind:value={course.semester}>
          </div>
        </form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" on:click={() => dispatch('close')}>Cancel</button>
        <button type="button" class="btn btn-primary" on:click={handleSubmit} disabled={isSubmitting}>
          {#if isSubmitting}
            <span class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
            Adding...
          {:else}
            Add Course
          {/if}
        </button>
      </div>
    </div>
  </div>
</div>

<style>
  .modal {
    background-color: rgba(0,0,0,0.5);
  }
</style>