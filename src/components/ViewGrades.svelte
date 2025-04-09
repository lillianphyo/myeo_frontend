<script>
  import { createEventDispatcher, onMount } from 'svelte';
  import { jwt } from '../stores/auth';
  
  const dispatch = createEventDispatcher();
  export let course;
  
  let grades = [];
  let filteredGrades = [];
  let searchTerm = '';
  let sortBy = 'student_name';
  let sortDirection = 'asc';
  let isLoading = true;
  let error = null;

  // Reactive statement to fetch grades when course changes
  $: {
    if (course && course.id) {
      fetchGrades();
    }
  }

  async function fetchGrades() {
    isLoading = true;
    error = null;
    try {
      const response = await fetch(`http://localhost:5000/course/${course.id}/grades`, {
        headers: { 'Authorization': `Bearer ${$jwt}` }
      });
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      
      grades = await response.json();
      filteredGrades = [...grades];
      sortGrades();
    } catch (err) {
      console.error('Failed to fetch grades:', err);
      error = err.message || 'Failed to load grades';
    } finally {
      isLoading = false;
    }
  }
  
  function filterGrades() {
    if (!searchTerm) {
      filteredGrades = [...grades];
    } else {
      const term = searchTerm.toLowerCase();
      filteredGrades = grades.filter(g => 
        (g.student_name && g.student_name.toLowerCase().includes(term)) || 
        (g.student_id && g.student_id.toLowerCase().includes(term)) ||
        (g.grade && g.grade.toLowerCase().includes(term))
      );
    }
    sortGrades();
  }
  
  function sortGrades() {
    filteredGrades.sort((a, b) => {
      // Handle "Not Graded" at bottom
      if (a.grade === 'Not Graded') return 1;
      if (b.grade === 'Not Graded') return -1;
      
      let comparison = 0;
      if (sortBy === 'student_name') {
        comparison = (a.student_name || '').localeCompare(b.student_name || '');
      } else if (sortBy === 'student_id') {
        comparison = (a.student_id || '').localeCompare(b.student_id || '');
      } else if (sortBy === 'grade') {
        comparison = (a.grade || '').localeCompare(b.grade || '');
      }
      
      return sortDirection === 'asc' ? comparison : -comparison;
    });
  }
  
  function handleSort(column) {
    if (sortBy === column) {
      sortDirection = sortDirection === 'asc' ? 'desc' : 'asc';
    } else {
      sortBy = column;
      sortDirection = 'asc';
    }
    sortGrades();
  }
  
  function exportToCSV() {
    try {
      const headers = ['Student ID', 'Student Name', 'Grade', 'Comments'];
      const csvContent = [
        headers.join(','),
        ...filteredGrades.map(g => 
          [g.student_id, g.student_name, g.grade, g.comments || '']
            .map(field => `"${(field || '').toString().replace(/"/g, '""')}"`)
            .join(',')
        )
      ].join('\n');
      
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const link = document.createElement('a');
      link.href = url;
      link.setAttribute('download', `${course.name.replace(/[^a-z0-9]/gi, '_')}_grades.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    } catch (err) {
      console.error('Export failed:', err);
      error = 'Failed to export grades';
    }
  }
</script>

<div class="grade-view-container">
  <div class="card">
    <div class="card-header bg-primary text-white">
      <div class="d-flex justify-content-between align-items-center">
        <h5 class="mb-0">Grades for {course.name}</h5>
        <button class="btn btn-sm btn-light" on:click={() => dispatch('close')}>
          <i class="bi bi-x-lg"></i>
        </button>
      </div>
    </div>
    
    <div class="card-body position-relative">
      {#if isLoading}
        <div class="loading-overlay">
          <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
          </div>
        </div>
      {:else if error}
        <div class="alert alert-danger">
          {error}
          <button class="btn btn-sm btn-outline-danger ms-2" on:click={fetchGrades}>
            Retry
          </button>
        </div>
      {:else}
        <div class="row mb-3">
          <div class="col-md-6">
            <div class="input-group">
              <span class="input-group-text">
                <i class="bi bi-search"></i>
              </span>
              <input
                type="text"
                class="form-control"
                placeholder="Search students..."
                bind:value={searchTerm}
                on:input={filterGrades}
              >
            </div>
          </div>
          <div class="col-md-6 text-end">
            <button class="btn btn-success" on:click={exportToCSV}>
              <i class="bi bi-download me-2"></i>Export CSV
            </button>
          </div>
        </div>
        
        {#if filteredGrades.length > 0}
          <div class="table-responsive grade-table">
            <table class="table table-hover">
              <thead>
  <tr>
    <th on:click={() => handleSort('student_id')} class:active={sortBy === 'student_id'}>
      Student ID
      <i class:bi-chevron-up={sortBy === 'student_id' && sortDirection === 'asc'}
         class:bi-chevron-down={sortBy === 'student_id' && sortDirection === 'desc'}
         class:bi={sortBy !== 'student_id'}></i>
    </th>
    <th on:click={() => handleSort('student_name')} class:active={sortBy === 'student_name'}>
      Student Name
      <i class:bi-chevron-up={sortBy === 'student_name' && sortDirection === 'asc'}
         class:bi-chevron-down={sortBy === 'student_name' && sortDirection === 'desc'}
         class:bi={sortBy !== 'student_name'}></i>
    </th>
    <th on:click={() => handleSort('grade')} class:active={sortBy === 'grade'}>
      Grade
      <i class:bi-chevron-up={sortBy === 'grade' && sortDirection === 'asc'}
         class:bi-chevron-down={sortBy === 'grade' && sortDirection === 'desc'}
         class:bi={sortBy !== 'grade'}></i>
    </th>
    <th>Comments</th>
  </tr>
</thead>
              <tbody>
                {#each filteredGrades as grade (grade.id)}
                  <tr class:table-warning={grade.grade === 'Not Graded'}>
                    <td>{grade.student_id}</td>
                    <td>{grade.student_name}</td>
                    <td>
                      {#if grade.grade !== 'Not Graded'}
                        <span class="badge rounded-pill bg-{
                          grade.grade === 'A' ? 'success' : 
                          grade.grade === 'B' ? 'info' : 
                          grade.grade === 'C' ? 'warning' : 'danger'
                        }">
                          {grade.grade}
                        </span>
                      {:else}
                        <span class="badge rounded-pill bg-secondary">Not Graded</span>
                      {/if}
                    </td>
                    <td>{grade.comments || '-'}</td>
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        {:else}
          <div class="alert alert-warning">
            {#if searchTerm}
              No students match your search
            {:else}
              No students found in this course
            {/if}
          </div>
        {/if}
      {/if}
    </div>
  </div>
</div>

<style>
  .grade-view-container {
    position: relative;
    min-height: 300px;
  }
  
  .loading-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(255, 255, 255, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }
  
  .grade-table th {
    cursor: pointer;
    position: relative;
  }
  
  .grade-table th:hover {
    background-color: rgba(0, 0, 0, 0.03);
  }
  
  .grade-table th.active {
    background-color: rgba(0, 0, 0, 0.05);
  }
  
  .grade-table th i {
    margin-left: 0.5rem;
    opacity: 0.5;
  }
  
  .grade-table th.active i {
    opacity: 1;
  }
  
  .badge {
    font-size: 0.85em;
    min-width: 2.5em;
  }
  
  .table-warning {
    --bs-table-bg: rgba(255, 193, 7, 0.1);
  }
</style>