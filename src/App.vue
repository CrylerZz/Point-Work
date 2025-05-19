<template>
    <div class="min-h-screen bg-black text-white flex flex-col font-sans">
        <!-- HEADER -->
        <header class="flex items-center justify-between flex-col md:flex-row w-full mx-auto p-4 gap-4 md:gap-6">
            <!-- Titre + Nav à gauche -->
            <div class="flex items-center justify-between md:justify-start gap-4 md:gap-8 w-full">
                <h1 class="text-xl md:text-2xl font-extrabold tracking-tight whitespace-nowrap">Point&Work</h1>
                <nav class="flex bg-[#222222] rounded-full md:px-3 md:py-2">
                    <button
                        @click="showArchived = false"
                        :class="[
              showArchived ? 'text-[#bbbbbb]' : 'bg-white text-black',
              'rounded-full px-5 py-2 text-sm font-semibold transition whitespace-nowrap'
            ]"
                    >
                        Projets actifs
                    </button>
                    <button
                        @click="showArchived = true"
                        :class="[
              showArchived ? 'bg-white text-black' : 'text-[#bbbbbb]',
              'rounded-full px-5 py-2 text-sm font-semibold transition whitespace-nowrap'
            ]"
                    >
                        Projets archivés
                    </button>
                </nav>
            </div>

            <!-- Ajout projet à droite -->
            <form
                @submit.prevent="addProject"
                class="flex items-center w-full justify-end h-14"
            >
                <input
                    v-model="newProjectName"
                    type="text"
                    placeholder="Nom du projet"
                    class="rounded-l-2xl px-4 h-12 md:h-14 bg-[#1a1a1a] text-white placeholder:text-neutral-500 shadow-inner focus:outline-none w-full max-w-full md:max-w-sm"
                />
                <button
                    type="submit"
                    class="bg-green-600 text-white hover:bg-green-700  font-semibold px-5 h-12 md:h-14 rounded-r-2xl transition whitespace-nowrap"
                >
                    Ajouter
                </button>
            </form>
        </header>

        <!-- Liste des projets -->
        <div class=" grow w-full  p-4 mx-auto overflow-auto">
            <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4">
            <div
                v-for="project in filteredProjects"
                :key="project.id"
                class="md:overflow-hidden  h-fit w-full group bg-gradient-to-tr from-[#1a1a1a] to-[#2c2c2c]  rounded-2xl flex flex-col gap-5 relative"
            >
                <div class="flex md:hidden group-hover:flex items-center justify-center gap-4 absolute inset-0 md:bg-black/30 md:backdrop-blur-sm w-full mx-auto">
                    <button
                        @click="toggleArchive(project.id)"
                        class="text-xs text-white bg-amber-400 hover:bg-amber-500 cursor-pointer w-8 md:w-10 h-8 md:h-10 flex items-center justify-center rounded transition">
                        <svg class="h-4" viewBox="0 0 20 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path fill-rule="evenodd" clip-rule="evenodd" d="M19.5668 2.50277C19.5668 1.12205 18.4475 0.00276696 17.0668 0.00276696H2.60681C1.33819 -0.0566811 0.228114 0.848167 0.0305656 2.10272C-0.166982 3.35728 0.611309 4.55948 1.83681 4.89277V13.8928C1.8368 15.7445 3.33504 17.2473 5.18681 17.2528H14.4668C15.3597 17.2554 16.2169 16.9026 16.8492 16.2722C17.4814 15.6418 17.8368 14.7856 17.8368 13.8928V4.89277C18.8742 4.5603 19.575 3.59209 19.5668 2.50277ZM14.4768 15.7428H5.19681C4.70616 15.7428 4.23561 15.5479 3.88866 15.2009C3.54172 14.854 3.34681 14.3834 3.34681 13.8928V5.00277H16.3468V13.8928C16.3468 14.3869 16.1492 14.8605 15.7979 15.208C15.4466 15.5556 14.9709 15.7481 14.4768 15.7428ZM2.60681 3.50277H17.0668C17.6191 3.50277 18.0668 3.05505 18.0668 2.50277C18.0668 1.95048 17.6191 1.50277 17.0668 1.50277H2.60681C2.05453 1.50277 1.60681 1.95048 1.60681 2.50277C1.60681 3.05505 2.05453 3.50277 2.60681 3.50277Z" fill="white"/>
                            <path d="M12.0868 7.63277H7.58681C7.1726 7.63277 6.83681 7.96855 6.83681 8.38277C6.83681 8.79698 7.1726 9.13277 7.58681 9.13277H12.0868C12.501 9.13277 12.8368 8.79698 12.8368 8.38277C12.8368 7.96855 12.501 7.63277 12.0868 7.63277Z" fill="white"/>
                        </svg>
                    </button>
                    <button
                        @click="toggleTimer(project.id)"
                        class="w-12 md:w-16 h-12 md:h-16 cursor-pointer rounded-full flex items-center justify-center bg-green-500 text-black transition hover:bg-green-600"
                    >
                        <template v-if="isRunning(project.id)">
                            <svg class="h-4" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path d="M7.5 6.25C7.08804 6.25539 6.75539 6.58804 6.75 7V13C6.75 13.4142 7.08579 13.75 7.5 13.75C7.91421 13.75 8.25 13.4142 8.25 13V7C8.24461 6.58804 7.91196 6.25539 7.5 6.25Z" fill="white"/>
                                <path d="M12.5 6.25C12.088 6.25539 11.7554 6.58804 11.75 7V13C11.75 13.4142 12.0858 13.75 12.5 13.75C12.9142 13.75 13.25 13.4142 13.25 13V7C13.2446 6.58804 12.912 6.25539 12.5 6.25Z" fill="white"/>
                                <path fill-rule="evenodd" clip-rule="evenodd" d="M6 0H14C17.3137 0 20 2.68629 20 6V14C20 17.3137 17.3137 20 14 20H6C2.68629 20 0 17.3137 0 14V6C0 2.68629 2.68629 0 6 0ZM17.0919 17.0919C17.9113 16.2725 18.3681 15.1588 18.36 14V6C18.3681 4.84121 17.9113 3.72753 17.0919 2.90812C16.2725 2.08871 15.1588 1.63194 14 1.64H6C4.84121 1.63194 3.72754 2.08871 2.90813 2.90812C2.08872 3.72753 1.63195 4.84121 1.64 6V14C1.63195 15.1588 2.08872 16.2725 2.90813 17.0919C3.72754 17.9113 4.84121 18.3681 6 18.36H14C15.1588 18.3681 16.2725 17.9113 17.0919 17.0919Z" fill="white"/>
                            </svg>
                        </template>
                        <template v-else>
                            <svg class="h-4" viewBox="0 0 14 15" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path fill-rule="evenodd" clip-rule="evenodd" d="M5.10987 0.509956L12.3599 4.69996C13.2851 5.23665 13.8546 6.22535 13.8546 7.29496C13.8546 8.36456 13.2851 9.35326 12.3599 9.88996L5.10987 14.08C4.18136 14.616 3.03736 14.6158 2.10903 14.0795C1.1807 13.5431 0.609177 12.5521 0.609864 11.48V3.10996C0.609177 2.03782 1.1807 1.0468 2.10903 0.510437C3.03736 -0.0259309 4.18136 -0.0261142 5.10987 0.509956ZM4.20987 12.52L11.4599 8.33996C11.8329 8.12699 12.0622 7.72951 12.0599 7.29996C12.0637 6.8674 11.8345 6.46623 11.4599 6.24996L4.20987 2.06996C4.02854 1.96096 3.82142 1.90228 3.60987 1.89996C2.9416 1.89996 2.39987 2.44169 2.39987 3.10996V11.48C2.39987 11.8009 2.52735 12.1086 2.75427 12.3356C2.98119 12.5625 3.28896 12.69 3.60987 12.69C3.82142 12.6876 4.02854 12.629 4.20987 12.52Z" fill="white"/>
                            </svg>

                        </template>
                    </button>
                    <button @click="deleteProject(project.id)" class="text-xs text-white bg-red-500 hover:bg-red-600  cursor-pointer w-8 md:w-10 h-8 md:h-10 flex items-center justify-center rounded transition">
                        <svg class="h-4" viewBox="0 0 18 22" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M11.74 8L11.394 17M6.606 17L6.26 8M16.228 4.79C16.57 4.842 16.91 4.897 17.25 4.956M16.228 4.79L15.16 18.673C15.1164 19.2382 14.8611 19.7662 14.445 20.1512C14.029 20.5363 13.4829 20.7502 12.916 20.75H5.084C4.5171 20.7502 3.97102 20.5363 3.55498 20.1512C3.13894 19.7662 2.88359 19.2382 2.84 18.673L1.772 4.79M16.228 4.79C15.0739 4.61552 13.9138 4.4831 12.75 4.393M1.772 4.79C1.43 4.841 1.09 4.896 0.75 4.955M1.772 4.79C2.92613 4.61552 4.08623 4.4831 5.25 4.393M12.75 4.393V3.477C12.75 2.297 11.84 1.313 10.66 1.276C9.55362 1.24064 8.44638 1.24064 7.34 1.276C6.16 1.313 5.25 2.298 5.25 3.477V4.393M12.75 4.393C10.2537 4.20008 7.74628 4.20008 5.25 4.393" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                        </svg>
                    </button>
                </div>

                <div class="flex justify-between items-start p-6">
                    <h2 class="text-xl md:text-2xl font-bold leading-tight break-words">
                        {{ project.name }}
                    </h2>
                    <span class="text-xl md:text-2xl text-white">{{ formatTime(projectDisplayTime(project)) }}</span>

                </div>

            </div>
            </div>
        </div>
    </div>
</template>


<script setup>
import { ref, watch, onMounted, onUnmounted, computed } from 'vue'

const newProjectName = ref('')
const showArchived = ref(false)
const projects = ref([])
const intervalId = ref(null)

onMounted(() => {
    const saved = localStorage.getItem('projects')
    if (saved) {
        projects.value = JSON.parse(saved)
    }
    startInterval()
})

onUnmounted(() => {
    clearInterval(intervalId.value)
})

watch(
    projects,
    (val) => {
        localStorage.setItem('projects', JSON.stringify(val))
    },
    { deep: true }
)

function addProject() {
    if (!newProjectName.value.trim()) return

    projects.value.push({
        id: Date.now(),
        name: newProjectName.value.trim(),
        time: 0,
        runningSince: null,
        archived: false,
    })

    newProjectName.value = ''
}

function deleteProject(id) {
    const project = projects.value.find((p) => p.id === id)
    if (!project) return

    const confirmDelete = window.confirm(
        `Supprimer le projet « ${project.name} » ? Cette action est définitive.`
    )
    if (!confirmDelete) return

    projects.value = projects.value.filter((p) => p.id !== id)
}

function toggleTimer(id) {
    const project = projects.value.find((p) => p.id === id)
    if (!project) return

    if (project.runningSince) {
        const now = Date.now()
        const elapsed = Math.floor((now - project.runningSince) / 1000)
        project.time += elapsed
        project.runningSince = null
    } else {
        project.runningSince = Date.now()
    }
}

function isRunning(id) {
    const project = projects.value.find((p) => p.id === id)
    return project?.runningSince !== null
}

function projectDisplayTime(project) {
    if (!project.runningSince) return project.time
    const now = Date.now()
    const elapsed = Math.floor((now - project.runningSince) / 1000)
    return project.time + elapsed
}

function formatTime(seconds) {
    const h = Math.floor(seconds / 3600).toString().padStart(2, '0')
    const m = Math.floor((seconds % 3600) / 60).toString().padStart(2, '0')
    const s = (seconds % 60).toString().padStart(2, '0')
    return `${h}:${m}:${s}`
}

function startInterval() {
    intervalId.value = setInterval(() => {
        projects.value = [...projects.value]
    }, 1000)
}

function toggleArchive(id) {
    const project = projects.value.find((p) => p.id === id)
    if (project) {
        project.archived = !project.archived
    }
}

const filteredProjects = computed(() =>
    projects.value.filter((p) => p.archived === showArchived.value)
)

const INACTIVITY_LIMIT =  2 * 60 * 1000
let lastActivityTimestamp = Date.now()

// Met à jour le timestamp de dernière activité
function updateActivity() {
    lastActivityTimestamp = Date.now()
}

// Arrête tous les timers actifs
function stopAllTimers() {
    projects.value.forEach((project) => {
        if (project.runningSince) {
            const now = Date.now()
            const elapsed = Math.floor((now - project.runningSince) / 1000)
            project.time += elapsed
            project.runningSince = null
        }
    })
}

// Écoute des événements d’activité utilisateur
window.addEventListener('mousemove', updateActivity)
window.addEventListener('keydown', updateActivity)
window.addEventListener('scroll', updateActivity)
window.addEventListener('touchstart', updateActivity)

// Vérification périodique de l’inactivité
const inactivityChecker = setInterval(() => {
    if (Date.now() - lastActivityTimestamp >= INACTIVITY_LIMIT) {
        stopAllTimers()
    }
}, 10000)

// Nettoyage au démontage du composant
onUnmounted(() => {
    clearInterval(inactivityChecker)
    window.removeEventListener('mousemove', updateActivity)
    window.removeEventListener('keydown', updateActivity)
    window.removeEventListener('scroll', updateActivity)
    window.removeEventListener('touchstart', updateActivity)
})

</script>
