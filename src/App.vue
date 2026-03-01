<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Коллекция курсов CareerLab</h1>

    <!-- Форма добавления -->
    <div class="row mb-4">
      <div class="col-md-6 offset-md-3">
        <form @submit.prevent="addCourse" class="card p-3 shadow-sm">
          <input
            v-model="newTitle"
            type="text"
            class="form-control mb-2"
            placeholder="Название курса"
            required
          />

          <textarea
            v-model="newDescription"
            class="form-control mb-2"
            placeholder="Описание курса"
            required
          ></textarea>

          <input
  ref="fileInput"
  type="file"
  accept="image/*"
  class="form-control mb-3"
/>

          <button class="btn btn-primary w-100">
            Добавить курс
          </button>
        </form>
      </div>
    </div>

    <!-- Сортировка -->
    <div class="text-center mb-3">
      <button @click="sortCourses" class="btn btn-outline-secondary">
        Сортировать по названию
      </button>
    </div>

    <!-- Карточки -->
    <div class="row">
      <div
        v-for="course in courses"
        :key="course.id"
        class="col-md-4 mb-4"
      >
        <div class="card h-100 shadow-sm">
          <img
            :src="course.image"
            class="card-img-top"
            style="height:200px; object-fit:cover;"
          />

          <div class="card-body">
            <h5 class="card-title">{{ course.title }}</h5>
            <p class="card-text">{{ course.description }}</p>

            <button
              @click="removeCourse(course.id)"
              class="btn btn-danger btn-sm"
            >
              Удалить
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const courses = ref([])

onMounted(() => {
  const saved = localStorage.getItem('courses')
  if (saved) {
    courses.value = JSON.parse(saved)
  }
})

const newTitle = ref('')
const newDescription = ref('')
const fileInput = ref(null)

const addCourse = () => {
  const file = fileInput.value?.files[0]

  if (!newTitle.value || !newDescription.value || !file) {
    alert('Заполните все поля и выберите изображение')
    return
  }

  const imageUrl = URL.createObjectURL(file)

  const updatedCourses = [
    ...courses.value,
    {
      id: Date.now(),
      title: newTitle.value,
      description: newDescription.value,
      image: imageUrl
    }
  ]

  courses.value = updatedCourses
  localStorage.setItem('courses', JSON.stringify(updatedCourses))

  newTitle.value = ''
  newDescription.value = ''

  if (fileInput.value) {
    fileInput.value.value = ''
  }

  reader.readAsDataURL(file)
}

const removeCourse = (id) => {
  const updatedCourses = courses.value.filter(c => c.id !== id)
  courses.value = updatedCourses
  localStorage.setItem('courses', JSON.stringify(updatedCourses))
}

const sortCourses = () => {
  const updatedCourses = [...courses.value].sort((a, b) =>
    a.title.localeCompare(b.title)
  )
  courses.value = updatedCourses
  localStorage.setItem('courses', JSON.stringify(updatedCourses))
}
</script>