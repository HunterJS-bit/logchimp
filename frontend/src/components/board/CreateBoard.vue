<template>
	<Form>
		<l-text
			v-model="boardName.value"
			label="Name"
			type="text"
			name="Name"
			placeholder="Name of the board"
			:error="boardName.error"
			@keyup-enter="createBoard"
			@hide-error="hideBoardNameError"
		/>
		<div style="display: flex; justify-content: center;">
			<Button :loading="buttonLoading" @click="createBoard" type="primary">
				Create
			</Button>
		</div>
	</Form>
</template>

<script>
// packages
import axios from "axios";

// components
import Form from "../Form";
import LText from "../input/LText";
import Button from "../Button";

export default {
	name: "CreateBoard",
	data() {
		return {
			boardName: {
				value: "",
				error: {
					show: false,
					message: ""
				}
			},
			buttonLoading: false
		};
	},
	props: {
		redirect: {
			type: String,
			required: true
		}
	},
	components: {
		Form,
		LText,
		Button
	},
	methods: {
		hideBoardNameError(event) {
			this.boardName.error = event;
		},
		createBoard() {
			if (this.buttonLoading) {
				return;
			}
			if (this.boardName.value) {
				this.buttonLoading = true;
				const token = this.$store.getters["user/getAuthToken"];

				axios({
					method: "post",
					url: "/api/v1/boards",
					data: {
						name: this.boardName.value
					},
					headers: {
						Authorization: `Bearer ${token}`
					}
				})
					.then(() => {
						this.$router.push(this.redirect);
						this.buttonLoading = false;
					})
					.catch(error => {
						console.error(error);
						this.buttonLoading = false;
					});
			} else {
				this.boardName.error.show = true;
				this.boardName.error.message = "Required";
			}
		}
	}
};
</script>
