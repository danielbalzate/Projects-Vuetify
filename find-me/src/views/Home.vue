<template>
	<v-timeline dense>
		<v-timeline-item v-for="(lost, index) in losts" :key="index" large>
			<template v-slot:icon>
				<v-avatar>
					<img :src="lost.avatarUserPost" />
				</v-avatar>
			</template>
			<v-card class="elevation-2">
				<v-card-title class="headline">{{ lost.userPost }}</v-card-title>
				<v-card-text>
					{{ lost.messagePost }}
				</v-card-text>
			</v-card>
		</v-timeline-item>
	</v-timeline>
</template>

<script>
import {db} from "@/firebase";
import {mapState} from "vuex";

export default {
	data() {
		return {
			losts: []
		};
	},
	created() {
		this.getLostGlobal();
	},
	computed: {
		...mapState(["user"])
	},
	methods: {
		async getLostGlobal() {
			try {
				const allData = [];
				db.collection("users")
					.get()
					.then(function(querySnapshot) {
						querySnapshot.forEach(function(doc) {
							db.collection("users")
								.doc(doc.id)
								.collection("lostPet")
								.get()
								.then(function(querySnapshot) {
									querySnapshot.forEach(function(res) {
										const registerDate = new Date(res.data().registerDate.seconds * 1000)
											.toISOString()
											.slice(-30, -8)
											.replace("T", " ");
										const lostGlobal = {
											id: res.id,
											avatarUserPost: res.data().avatarUserPost,
											userPost: res.data().userPost,
											userMail: res.data().userMail,
											userUid: res.data().userUid,
											titlePost: res.data().titlePost,
											imgPost: res.data().imgPost,
											messagePost: res.data().messagePost,
											registerDate: registerDate
										};
										allData.push(lostGlobal);
									});
								});
						});
					});
				this.losts = allData;
			} catch (error) {
				console.log("TCL: getLostGlobal -> error", error);
			}
		}
	}
};
</script>
