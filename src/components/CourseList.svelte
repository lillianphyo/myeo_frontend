<script>
  import { onMount } from 'svelte';
  import AddCourseModal from './AddCourseModal.svelte';
  import RegisterStudentModal from './RegisterStudentModal.svelte';
  import UploadGradesModal from './UploadGradesModal.svelte';
  import ViewGrades from './ViewGrades.svelte';
  import { jwt } from '../stores/auth';
  
  let courses = [];
  let showAddCourseModal = false;
  let showRegisterModal = false;
  let showUploadModal = false;
  let showViewGrades = false;
  let selectedCourse = null;
  
  onMount(async () => {
    await fetchCourses();
  });
  
  async function fetchCourses() {
    const response = await fetch('http://localhost:5000/course', {
      headers: { 'Authorization': `Bearer ${$jwt}` }
    });
    courses = await response.json();
  }
  
  function openRegisterModal(course) {
    selectedCourse = course;
    showRegisterModal = true;
  }
  
  function openUploadModal(course) {
    selectedCourse = course;
    showUploadModal = true;
  }
  
  function openViewGrades(course) {
    selectedCourse = course;
    showViewGrades = true;
  }
</script>

<div class="d-flex justify-content-between align-items-center mb-4">
  <h2>Course List</h2>
  <button class="btn btn-primary" on:click={() => showAddCourseModal = true}>
    <i class="bi bi-plus-circle me-2"></i>New Course
  </button>
</div>

{#if courses.length > 0}
  <div class="table-responsive">
    <table class="table table-hover table-striped">
      <thead class="table-dark">
        <tr>
          <th>Course ID</th>
          <th>Course Name</th>
          <th>Instructor</th>
          <th>Semester</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        {#each courses as course}
          <tr>
            <td>{course.course_id}</td>
            <td>{course.name}</td>
            <td>{course.instructor}</td>
            <td>{course.semester}</td>
            <td>
              <div class="btn-group" role="group">
                <button class="btn btn-sm btn-outline-success" on:click={() => openRegisterModal(course)}>
                  Register Student
                </button>
                <button class="btn btn-sm btn-outline-info" on:click={() => openUploadModal(course)}>
                  Upload Grades
                </button>
                <button class="btn btn-sm btn-outline-primary" on:click={() => openViewGrades(course)}>
                  View Grades
                </button>
              </div>
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{:else}
  <div class="alert alert-info">No courses found. Add a new course to get started.</div>
{/if}

{#if showAddCourseModal}
  <AddCourseModal 
    on:close={() => showAddCourseModal = false} 
    on:courseAdded={fetchCourses}
  />
{/if}

{#if showRegisterModal}
  <RegisterStudentModal 
    course={selectedCourse} 
    on:close={() => showRegisterModal = false}
  />
{/if}

{#if showUploadModal}
  <UploadGradesModal 
    course={selectedCourse} 
    on:close={() => showUploadModal = false}
  />
{/if}

{#if showViewGrades}
  <ViewGrades 
    course={selectedCourse} 
    on:close={() => showViewGrades = false}
  />
{/if}

<style>
  .table-responsive {
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 15px rgba(0,0,0,0.1);
  }
  
  .btn-group .btn {
    margin-right: 5px;
  }
</style>