<script lang="ts">
	import type { GestureRecognizerResult } from '@mediapipe/tasks-vision';
	import WebcamVideo from '$lib/WebcamVideo.svelte';
	import { startRecognition } from '$lib/recognizer';

	let videoElem: HTMLVideoElement;
	let buttonElem: HTMLButtonElement;
	let isWebcamEnabled = false;

	$: if (isWebcamEnabled) {
		startRecognition(videoElem, processResult);
		buttonElem.disabled = true;
		//buttonElem.style.display = 'none'; // 有効化したらボタンを消す
	}

	// 以下は認識結果の表示用
	let categoryName1: string = '?'; // 1 つ目の手の形状（カテゴリ）
	let categoryName2: string = '?'; // 2 つ目の手の形状（カテゴリ）
	let score1 = 0.0; // 1 つ目のカテゴリのスコア
	let score2 = 0.0; // 2 つ目のカテゴリのスコア

	function processResult(result: GestureRecognizerResult) {
		const category1 = result.gestures.at(0)?.at(0);
		const category2 = result.gestures.at(1)?.at(0);
		categoryName1 = category1?.categoryName ?? '?';
		categoryName2 = category2?.categoryName ?? '?';
		score1 = category1?.score ?? 0.0;
		score2 = category2?.score ?? 0.0;
	}
</script>

<h1>MediaPipe Demo</h1>
<div>
	<WebcamVideo bind:videoElem active={isWebcamEnabled} />
</div>
<button bind:this={buttonElem} on:click={() => (isWebcamEnabled = true)}
	>Web カメラによる認識を開始</button
>
<ul>
	<li>手の形1: {categoryName1} (信頼度: {score1.toFixed(2)})</li>
	<li>手の形2: {categoryName2} (信頼度: {score2.toFixed(2)})</li>
</ul>
