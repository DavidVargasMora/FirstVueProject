<template>
    <Layout>
        <template #header>
            <Header>

            </Header>
        </template>
        <template #resume>
            <Resume 
                :label="label"
                :total-amount = "totalAmount"
                :amount="amount"
            >
                <template #graphic>
                    <Graphic :amounts="amounts"
                    @select = "select" />
                </template>
                <template #action>
                    <Action @create="create"/>
                </template>
            </Resume>
        </template>
        <template #movements>
            <Movements
                :movements="movements"
                @remove = "remove"
            />
        </template>
    </Layout>
</template>
<script>
    import Layout from "./Layout.vue";
    import Header from "./Header.vue";
    import Resume from "./Resume/Index.vue";
    import Movements from "./Movements/Index.vue";
    import Action from "./Action.vue";
    import Graphic from "./Resume/Graphic.vue"

    export default {
        components: {
            Layout,
            Header,
            Resume,
            Movements,
            Action,
            Graphic
        },
        data() {
            return {
                amount: null,
                label: null,
                movements: []
            }
        },
        computed: {
            amounts() {
/*                 const lastDays = this.movements.filter(m => {
                    const today = new Date();
                    console.log(today);
                    const oldDate = today.setDate(today.getDate() - 15);
                    console.log(oldDate);
                    console.log(m.time);
                    return m.time <= oldDate; 
                })
                .map(m => m.amount) */

                const lastDays = this.movements
                .map(m => m.amount)

                return lastDays.map( (m, i) => {
                    const lastMovements = lastDays.slice(0, i + 1);

                    return m + lastMovements.reduce((suma, movement) => {
                        return suma + movement
                    }, 0);
                })
            },
            totalAmount() {
                return this.movements.reduce((suma, m) => {
                    return suma + m.amount;
                }, 0)
            }
        },
        mounted() {
            const movements = JSON.parse(localStorage.getItem("movements"));
            if (Array.isArray(movements)) {
                this.movements = movements.map(m => {
                    return { ...m, time: new Date(m.time)};
                });
            }
        },
        methods: {
            create(movement) {
                this.movements.push(movement);
                this.save();
            },
            remove(id) {
                let index = this.movements.findIndex(m => m.id === id);
                this.movements.splice(index, 1);
                this.save();
            },
            save() {
                localStorage.setItem("movements", JSON.stringify(this.movements));
            },
            select(el) {
                console.log(el);
                this.amount = el;
            }
        }
    }
</script>