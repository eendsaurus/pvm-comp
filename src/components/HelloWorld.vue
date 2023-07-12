<template>
    <h1>All drops rewarding points during this bingo</h1>
    <label>{{ searchInput }}</label>
    <div class="search-container">
        <input
            v-model="searchInput"
            type="text"
            class="search-input"
            placeholder="Search for any specific boss or drop"
            @change="filterBossRows"
        />
    </div>

    <div class="boss-container">
        <div class="boss-row" v-for="(row, index) in bossRows" :key="index">
            <div
                v-for="(boss, bossIndex) in row"
                :key="bossIndex"
                class="boss-card"
            >
                <h2>{{ boss.name }}</h2>

                <!-- <img :src="boss.img" :alt="boss.name" /> -->

                <ul>
                    <li
                        v-for="(drop, dropIndex) in boss.drops"
                        :key="dropIndex"
                    >
                        {{ drop.name }} ({{ drop.points }} points)
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { computed, ref, Ref } from "vue";
    import data from "../../data.json";

    interface Boss {
        name: string;
        nicknames?: string[];
        img: string;
        drops: {
            name: string;
            aka?: string[];
            points: number;
        }[];
    }

    const searchInput = ref("");
    const bosses: Ref<Boss[]> = ref(data.bosses);
    const bossesFiltered: Ref<Boss[]> = computed(() => {
        const searchValue = searchInput.value.toLowerCase();
        return bosses.value.filter((boss) => {
            return (
                boss.name.toLowerCase().includes(searchValue) ||
                boss.nicknames?.some((nickname) =>
                    nickname.toLowerCase().includes(searchValue)
                ) ||
                boss.drops.some(
                    (drop) =>
                        drop.name.toLowerCase().includes(searchValue) ||
                        drop.aka?.some((alias) =>
                            alias.toLowerCase().includes(searchValue)
                        )
                )
            );
        });
    });
    const bossRows = computed(() => {
        const rows = [];
        let currentRow = [];

        for (let i = 0; i < bossesFiltered.value.length; i++) {
            currentRow.push(bossesFiltered.value[i]);

            if ((i + 1) % 4 === 0) {
                rows.push(currentRow);
                currentRow = [];
            }
        }

        if (currentRow.length > 0) {
            rows.push(currentRow);
        }

        return rows;
    });

    const filterBossRows = () => {};
</script>

<style scoped>
    .boss-container {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
    }

    .boss-row {
        display: flex;
        justify-content: space-between;
        width: 100%;
    }

    .boss-card {
        flex-basis: 23%;
        margin-bottom: 20px;
        padding: 10px;
        margin-right: 20px;
        border: 1px solid #ccc;
        text-align: center;
    }

    ul,
    li {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .search-container {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 80px;
    }

    .search-input {
        border: none;
        outline: none;
        font-size: 16px;
        width: 300px;
        height: 40px;
        border: 1px solid #ccc;
        border-radius: 20px;
        padding: 0 10px;
    }
</style>
