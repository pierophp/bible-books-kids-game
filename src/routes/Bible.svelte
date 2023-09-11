<script lang="ts">
	import { bibleBooks } from './biibleBooks';
	import { chapters } from './chapters';

	const booksChapters = Object.values(chapters);

	type BibleBook = {
		code: string;
		title: string;
		position: number;
	};

	function removeAccents(str: string): string {
		return str.normalize('NFD').replace(/\p{Diacritic}/gu, '');
	}

	function clean() {
		selectedLetter = null;
		selectedBook = null;
		selectedChapter = null;
		selectedVerse = null;
	}

	function confirm() {
		if (selectedVerse) {
			history = [`${selectedBook.title} ${selectedChapter}:${selectedVerse}`, ...history];
		}
		clean();
	}

	function selectChapter(chapter: number) {
		const totalVerses = Object.values(booksChapters[selectedBook.position]).find(
			(item) => item.c === chapter
		).t;

		verses = Array.from(Array(totalVerses).keys()).map((k) => k + 1);

		selectedChapter = chapter;
	}

	function selectVerse(verse: number) {
		selectedVerse = verse;
	}

	const books: BibleBook[] = bibleBooks.map((bibleBook, i) => ({
		code: removeAccents(bibleBook),
		title: bibleBook,
		position: i
	}));

	const bibleBooksMap = new Map<string, BibleBook[]>();

	for (const book of books) {
		const letter = book.code[0];

		bibleBooksMap.set(letter, [...(bibleBooksMap.get(letter) ?? []), book]);
	}

	const letters = ['1', '2', '3'];
	for (let i = 65; i <= 90; i++) {
		letters.push(String.fromCharCode(i));
	}

	let selectedLetter: string | null = null;
	let selectedBook: BibleBook | null = null;
	let selectedChapter: number | null = null;
	let selectedVerse: number | null = null;
	let history: string[] = [];

	let verses: number[] = [];
</script>

<h1 class="p-3 text-3xl font-bold bg-purple-600 border-b-2 border-purple-800 text-white">
	{#if selectedBook}
		{selectedBook.title} {selectedChapter ?? ''}{selectedVerse ? `:${selectedVerse}` : ''}
	{:else if selectedLetter}
		{selectedLetter}
	{:else}
		Escolha uma Inicial
	{/if}
</h1>

<div class="p-2">
	<button
		class="px-5 py-1 my-2 border-2 bg-purple-800 hover:bg-purple-700 border-purple-900 text-white"
		on:click={clean}>Voltar</button
	>

	<div class="flex gap-2 flex-wrap max-w-4xl">
		{#if selectedChapter}
			{#each verses as verse}
				<button
					class="w-10 h-10 border-2 bg-purple-400 hover:bg-purple-800 border-purple-500 text-white flex items-center justify-center disabled:opacity-75 disabled:bg-gray-500 disabled:border-gray-700"
					on:click={() => selectVerse(verse)}
				>
					{verse}
				</button>
			{/each}
		{:else if selectedBook}
			{#each Object.values(booksChapters[selectedBook.position]).map((item) => item.c) as chapter}
				<button
					class="w-10 h-10 border-2 bg-purple-600 hover:bg-purple-800 border-purple-700 text-white flex items-center justify-center disabled:opacity-75 disabled:bg-gray-500 disabled:border-gray-700"
					on:click={() => selectChapter(chapter)}
				>
					{chapter}
				</button>
			{/each}
		{:else if selectedLetter}
			{#each bibleBooksMap.get(selectedLetter) as bibleBook}
				<button
					class="px-5 py-1 border-2 bg-purple-300 border-purple-400 hover:bg-purple-400 text-purple-800 flex items-center justify-center"
					on:click={() => (selectedBook = bibleBook)}
				>
					{bibleBook.title}
				</button>
			{/each}
		{:else}
			{#each letters as letter}
				<button
					class="w-10 h-10 border-2 bg-purple-600 hover:bg-purple-800 border-purple-700 text-white flex items-center justify-center disabled:opacity-75 disabled:bg-gray-500 disabled:border-gray-700"
					disabled={!bibleBooksMap.has(letter)}
					on:click={() => (selectedLetter = letter)}
				>
					{letter}
				</button>
			{/each}
		{/if}
	</div>
	{#if selectedVerse}
		<button
			on:click={confirm}
			class="px-5 py-1 my-2 border-2 bg-emerald-500 hover:bg-emerald-600 border-emerald-700 text-white"
			>Confirmar</button
		>
	{/if}
	{#if history.length > 0}
		<div class="my-5 border-t border-purple-600">
			<div class="mt-5 text-xl font-bold">Histórico</div>
			{#each history as item}
				<div>{item}</div>
			{/each}
		</div>
	{/if}
</div>
