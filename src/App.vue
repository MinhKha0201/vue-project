<script setup lang="ts">
import {
    Table,
    TableBody,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
} from '@/components/ui/table'
import { onMounted, ref } from 'vue'
import TeamModal from './components/TeamModal.vue'
import AppModal from './components/AppModal.vue'
import type { Team } from './types/Team.type'
import type { User } from './types/User.type'
import type { App } from './types/App.type'

const usersApi: string = 'https://admob.lutech.vn/api/users'
const teamsApi: string = 'https://admob.lutech.vn/api/teams'
const appsApi: string = 'https://admob.lutech.vn/api/apps'

type Response = {
    current_page: number
    data: User[]
    first_page_url: string
    from: number | null
    next_page_url: null
    path: string | null
    per_page: number | null
    prev_page_url: string | null
    to: number | null
}

const fetchUser = async (
    url: string,
    options?: RequestInit,
): Promise<Response> => {
    return (await fetch(url, options)).json()
}

const fetchTeam = async (
    url: string,
    options?: RequestInit,
): Promise<Team[]> => {
    return (await fetch(url, options)).json()
}
const fetchApp = async (url: string, options?: RequestInit): Promise<App[]> => {
    return (await fetch(url, options)).json()
}
const next_page_url = ref<string | null>(null)
const usersData = ref<User[]>([])
const teamsData = ref<Team[]>([])
const appsData = ref<App[]>([])
const fetchData = async () => {
    try {
        do {
            const usersRes = await fetchUser(
                next_page_url.value !== null ? next_page_url.value : usersApi,
            )
            usersData.value = [...usersData.value, ...usersRes.data]
            next_page_url.value = usersRes.next_page_url
        } while (next_page_url.value !== null)
        teamsData.value = await fetchTeam(teamsApi)
        appsData.value = await fetchApp(appsApi)
    } catch (error) {
        console.log(error)
    }
}
onMounted(fetchData)
</script>

<template>
    <div class="w-full min-h-screen grid place-items-center">
        <div class="w-1/2">
            <Table>
                <TableHeader>
                    <TableRow>
                        <TableHead class="w-[100px]"> ID </TableHead>
                        <TableHead>Name</TableHead>
                        <TableHead>Teams</TableHead>
                        <TableHead> Apps </TableHead>
                    </TableRow>
                </TableHeader>
                <TableBody>
                    <TableRow v-for="user in usersData" :key="user.id">
                        <TableCell class="font-medium">
                            {{ user.id }}
                        </TableCell>
                        <TableCell>{{ user.name }}</TableCell>
                        <TableCell
                            ><TeamModal
                                :user-id="user.id"
                                :teams="teamsData"
                                :teamsSelected="user.teams"
                        /></TableCell>
                        <TableCell>
                            <AppModal :user-id="user.id" :apps="appsData"
                        /></TableCell>
                    </TableRow>
                </TableBody>
            </Table>
        </div>
    </div>
</template>
