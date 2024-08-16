<script setup lang="ts">
import { Button } from '@/components/ui/button'
import {
    Dialog,
    DialogContent,
    DialogDescription,
    DialogFooter,
    DialogHeader,
    DialogTitle,
    DialogTrigger,
} from '@/components/ui/dialog'
import {
    TableBody,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
} from '@/components/ui/table'
import type { Team } from '../types/Team.type'
import { Checkbox } from './ui/checkbox'
import TableCustom from '@/components/Custom/TableCustom.vue'
import { onMounted, ref } from 'vue'

type Prop = {
    userId: number
    teams: Team[]
    teamsSelected: Team[]
}

const props = defineProps<Prop>()

const data = ref<(Team & { isChecked: boolean })[]>([])
const isCheckedAll = ref<boolean>(true)
const isOpen = ref<boolean>(false)

const handleCheckbox = (id: number) => {
    const team = data.value.find((t) => t.id === id)
    if (team) {
        team.isChecked = !team.isChecked
    }
    for (let team of data.value) {
        isCheckedAll.value = true
        if (team.isChecked === false) {
            isCheckedAll.value = false
            break
        }
    }
}
const handleCheckboxAll = () => {
    isCheckedAll.value = !isCheckedAll.value
    if (isCheckedAll.value) {
        data.value = data.value.map((team) => {
            return { ...team, isChecked: true }
        })
    } else {
        data.value = data.value.map((team) => {
            return { ...team, isChecked: false }
        })
    }
}

onMounted(() => {
    data.value = props.teams.map((team) => {
        return {
            ...team,
            isChecked: props.teamsSelected.some((t) => t.id === team.id),
        }
    })
    for (let team of data.value) {
        if (team.isChecked === false) {
            isCheckedAll.value = false
            break
        }
        isCheckedAll.value = true
    }
})

const handleClick = () => {
    isOpen.value = false
    console.log(data.value)
}
</script>

<template lang="">
    <Dialog @update:open="isOpen">
        <DialogTrigger as-child>
            <Button variant="outline" class="w-[100px]">
                {{
                    teamsSelected.length > 0
                        ? teamsSelected.length + ' ' + 'Selected'
                        : 'Select Teams'
                }}
            </Button>
        </DialogTrigger>
        <DialogContent
            class="sm:max-w-[425px] grid-rows-[auto_minmax(0,1fr)_auto] p-0 max-h-[90dvh]"
        >
            <DialogHeader class="p-6 pb-0">
                <DialogTitle>Teams</DialogTitle>
                <DialogDescription> Select teams </DialogDescription>
            </DialogHeader>
            <TableCustom class="max-w-[500px]">
                <TableHeader>
                    <TableRow>
                        <TableHead class="w-[100px]">
                            <Checkbox
                                :checked="isCheckedAll"
                                @update:checked="handleCheckboxAll"
                            />
                        </TableHead>
                        <TableHead>ID</TableHead>
                        <TableHead>Name</TableHead>
                    </TableRow>
                </TableHeader>
                <TableBody>
                    <TableRow v-for="team in data" :key="team.id">
                        <TableCell class="font-medium">
                            <Checkbox
                                :checked="team.isChecked"
                                @update:checked="handleCheckbox(team.id)"
                            />
                        </TableCell>
                        <TableCell>{{ team.id }}</TableCell>
                        <TableCell>{{ team.name }}</TableCell>
                    </TableRow>
                </TableBody>
            </TableCustom>
            <DialogFooter class="p-6 pt-0">
                <Button @click="handleClick" type="submit">
                    Save changes
                </Button>
            </DialogFooter>
        </DialogContent>
    </Dialog>
</template>
