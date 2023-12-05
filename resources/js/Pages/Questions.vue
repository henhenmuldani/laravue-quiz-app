<template>
    <Layout>
        <button @click="createQuestion" class="btn btn-success">Create</button>
        <table class="table table-striped">
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Question</th>
                    <th scope="col">Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(question, index) in questions">
                    <th scope="row">{{ index + 1 }}</th>
                    <td>{{ question.question }}</td>
                    <td>
                        <button
                            @click="viewQuestion(index)"
                            class="btn btn-primary"
                        >
                            View
                        </button>
                        <button
                            @click="
                                (editQuestionModal = true), editQuestion(index)
                            "
                            class="btn btn-success"
                        >
                            Edit
                        </button>
                        <button
                            @click="deleteQuestion(question.id)"
                            class="btn btn-danger"
                        >
                            Delete
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>

        <!-- Modal -->
        <Teleport to="body">
            <NewQuestionModal
                :show="showNewQuestionModal"
                @close="destroyModal"
            >
                <template #header>
                    <h5>Add New Question</h5>
                </template>
                <template #success>
                    <div v-if="success" class="alert alert-success">
                        {{ success }}
                    </div></template
                >
                <template #body
                    ><form>
                        <div class="mb-3">
                            <label for="exampleInputEmail1" class="form-label"
                                >Question</label
                            >
                            <input
                                type="text"
                                v-model="createdQuestion"
                                class="form-control"
                                id="exampleInputEmail1"
                                aria-describedby="emailHelp"
                            />
                        </div>
                        <table class="table table-striped">
                            <thead>
                                <tr>
                                    <th scope="col">#</th>
                                    <th scope="col">Answers</th>
                                    <th scope="col">Correct ?</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(answer, index) in newAnswers">
                                    <th scope="row">{{ answer.id }}</th>
                                    <td>
                                        <input
                                            type="text"
                                            v-model="answer.answer"
                                            class="form-control"
                                            id="exampleInputEmail1"
                                            aria-describedby="emailHelp"
                                        />
                                    </td>
                                    <td>
                                        <input
                                            :checked="
                                                answer.correct_answer === 1
                                            "
                                            class="form-check-input"
                                            :value="answer.id"
                                            @change="
                                                handleRadioToggle(answer.id)
                                            "
                                            type="radio"
                                        />
                                    </td>
                                </tr>
                            </tbody>
                        </table></form
                ></template>
                <template #footer>
                    <span @click="addNewAnswer" v-if="newAnswers.length < 4"
                        ><h3>+</h3></span
                    >
                    <button @click="destroyModal" class="btn btn-danger">
                        Close
                    </button>
                    <button
                        v-if="newAnswers.length > 3"
                        @click="submitQuestion"
                        class="btn btn-success"
                    >
                        Submit
                    </button>
                </template>
            </NewQuestionModal>

            <NewQuestionModal
                :show="showViewQuestionModal"
                @close="destroyModal"
            >
                <template #header>
                    <h5>View Question/Answers</h5>
                </template>
                <template #success>
                    <div v-if="success" class="alert alert-success">
                        {{ success }}
                    </div></template
                >
                <template #body>
                    <p>
                        <strong>Q. {{ selectedQuestion.question }}</strong>
                    </p>
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th scope="col">#</th>
                                <th scope="col">Answers</th>
                                <th scope="col">Correct ?</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(answer, index) in answers">
                                <th scope="row">{{ index + 1 }}</th>
                                <td>
                                    <input
                                        type="text"
                                        v-model="answer.answer"
                                        class="form-control"
                                        id="exampleInputEmail1"
                                        aria-describedby="emailHelp"
                                    />
                                </td>
                                <td>
                                    <input
                                        :checked="answer.correct_answer === 1"
                                        class="form-check-input"
                                        :value="answer.id"
                                        @change="handleRadioChange(answer.id)"
                                        type="radio"
                                    />
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </template>
                <template #footer>
                    <button @click="destroyModal" class="btn btn-danger">
                        Close
                    </button>
                    <button @click="updatedAnswers" class="btn btn-success">
                        Update
                    </button>
                </template>
            </NewQuestionModal>

            <NewQuestionModal
                :show="editQuestionModal"
                @close="editQuestionModal = false"
            >
                <template #header>
                    <h5>Edit Question</h5>
                </template>
                <template #success>
                    <div v-if="success" class="alert alert-success">
                        {{ success }}
                    </div>
                </template>
                <template #body>
                    <div class="mb-3">
                        <label for="exampleFormControlInput1" class="form-label"
                            >Question</label
                        >
                        <input
                            type="text"
                            v-model="questionForEdit.question"
                            class="form-control"
                            id="exampleFormControlInput1"
                            placeholder="name@example.com"
                        />
                    </div>
                </template>
                <template #footer>
                    <button
                        @click="editQuestionModal = false"
                        class="btn btn-danger"
                    >
                        Close
                    </button>
                    <button @click="updatedQuestion" class="btn btn-success">
                        Update
                    </button>
                </template>
            </NewQuestionModal>
        </Teleport>
    </Layout>
</template>

<script setup>
import Layout from "@/Shared/Layout.vue";
import NewQuestionModal from "@/Shared/NewQuestionModal.vue";
import { ref, computed } from "vue";
import { router, usePage } from "@inertiajs/vue3";

const page = usePage();
const success = computed(() => page.props.flash.success);

const showNewQuestionModal = ref(false);
const createdQuestion = ref(null);
const selectedAnswer = ref(null);
const newAnswers = ref([]);

const showViewQuestionModal = ref(false);
const selectedQuestion = ref(null);
const answers = ref([]);

// sementara untuk tampilan dulu
let answerId = 1;

const createQuestion = () => {
    showNewQuestionModal.value = true;
};

const destroyModal = () => {
    showNewQuestionModal.value = false;
    //TODO
    showViewQuestionModal.value = false;
};

const addNewAnswer = () => {
    const newAnswer = {
        id: answerId++,
        answer: "",
        correct_answer: 0,
    };
    newAnswers.value.push(newAnswer);
};

const handleRadioToggle = (id) => {
    selectedAnswer.value = id;
    newAnswers.value.forEach((answer) => {
        if (answer.id === id) {
            answer.correct_answer = 1;
        } else {
            answer.correct_answer = 0;
        }
    });
};

const validateAnswers = () => {
    for (const answer of newAnswers.value) {
        if (answer.answer.trim() === "") {
            return false;
        }
    }
    return true;
};

const answerCount = () => {
    if (newAnswers.length < 4) {
        alert("Answer must be 4");
    } else if (newAnswers.length === 4) {
        return true;
    }
    return false;
};

const submitQuestion = () => {
    if (!createdQuestion.value) {
        alert("Question is required");
        return false;
    }

    if (!validateAnswers() && !answerCount()) {
        alert("Fill all inputs");
        return false;
    }

    router.post("/questions", {
        question: createdQuestion.value,
        answers: newAnswers.value,
    });

    router.on("success", () => {
        createdQuestion.value = null;
        newAnswers.value = [];
    });
};

const props = defineProps({
    questions: Object,
    errors: Object,
});

const viewQuestion = (index) => {
    showViewQuestionModal.value = true;
    selectedQuestion.value = props.questions[index];
    answers.value = props.questions[index].answers;
};

//* handle radio change and submit edited answers
const selectedEditAnswer = ref(null);
const handleRadioChange = (id) => {
    selectedEditAnswer.value = id;
    answers.value.forEach((answer) => {
        if (answer.id === id) {
            answer.correct_answer = 1;
        } else {
            answer.correct_answer = 0;
        }
    });
};

//* Save edited answers
const updatedAnswers = () => {
    router.put("/answers", answers.value, (onSuccess) => {
        showViewQuestionModal.value = false;
    });
};

//* Edit question
const editQuestionModal = ref(false);
const questionForEdit = ref(null);
const editQuestion = (index) => {
    questionForEdit.value = props.questions[index];
};
const updatedQuestion = () => {
    router.put("/questions", questionForEdit.value, (onSuccess) => {
        editQuestionModal.value = false;
    });
};

//* Delete question
const deleteQuestion = (id) => {
    router.on("before", () => {
        return confirm("Are you sure?");
    });

    router.delete(`/questions/${id}`);
};
</script>

<style lang="scss" scoped></style>
