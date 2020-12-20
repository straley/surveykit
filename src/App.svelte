<script lang="ts">
import "smelte/src/tailwind.css"
import survey from "../public/survey.json"
import Question from "./components/Question.svelte"
import {
		Button,
	} from "smelte";

export let question: any
export let stack: string[] = []
export let answers: {[index: string]: any} = {}

function getStack() {
	try {
		return JSON.parse(localStorage.getItem("stack")) || []
	} catch(e) {
		return []
	}
}

function showQuestion(id:string) {
	if (!survey || !survey.questions || !survey.questions[id]) {
		return
	}

	if (stack[stack.length-1] !== id) {
		stack.push(id)
		localStorage.setItem("stack", JSON.stringify(stack))
	}

	question = survey.questions[id]
}

function showFirst() {
	if (!survey || !survey.first) {
		return
	}
	showQuestion(survey.first)
}

function goBack() {
	if (stack.length > 1) {
		stack.pop()
		const id = stack.pop()
		localStorage.setItem("stack", JSON.stringify(stack))
		showQuestion(id)
	}

}

stack = getStack()
if (stack && stack.length) {
	showQuestion(stack[stack.length-1])
} else {
	showFirst()
}


function onSelect(selection:string) {
	answers[stack[stack.length-1]] = selection
	localStorage.setItem("answers", JSON.stringify(answers))
	console.log(answers)

	if (question.logic) {
		for(const item of question.logic) {
			if (item.lessThan) {
				if (typeof selection === "number" && selection < item.lessThan) {
					showQuestion(item.next)
					break
				}
				continue				
			}
			if (item.greaterThan) {
				if (typeof selection === "number" && selection > item.greaterThan) {
					showQuestion(item.next)
					break
				}
				continue				
			}
			if (item.equals) {
				if (selection === item.equals) {
					showQuestion(item.next)
					break
				}
				continue				
			}
			showQuestion(item.next)
		} 
		return
	}
	showQuestion(question.next)
}
</script>

<main>
	<Question {...question} onselect={onSelect}/>
	{#if stack.length > 1}
	<Button on:click={goBack}>Back</Button>
	{/if}
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>