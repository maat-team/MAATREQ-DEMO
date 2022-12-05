
<head>
	<style>
		table,
		thead,
		th,
		td {
			padding: 10px;
			border: 1px solid black;
			border-collapse: collapse;
		}
		th {
			border: 1px solid;
			text-align: center;
		}
		td:nth-child(1) {
			border: 1px solid;
			font-weight:bold;
		}
	</style>
</head>


# Requirement Transformation Matrix

| Req ID |R0|R1|R2|R3|R4|R5|R6|R7|R8|R9|
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| R0 | = |&#8611; before | | | | | | | | |
| R1 |&#8627; upon | = |&#8611; before | |&#8626; goto | |&#8626; goto | | | |
| R2 | |&#8627; upon | = |&#8611; before | | | |&#8611; before | | |
| R3 | | |&#8627; upon | = |&#8826; scope |&#8826; scope<br/>&#8614; **deadline** |&#8826; scope | | | |
| R4 | |&#8624; goto | |&#8911; scope | = | |&#8611; before | | | |
| R5 | | | |&#8911; scope<br/>&#8615; **deadline** | | = |&#8611; before<br/>&#8626; **ref** | | | |
| R6 | |&#8624; goto | |&#8911; scope |&#8627; upon |&#8627; upon<br/>&#8624; **ref** | = | | | |
| R7 | | |&#8627; upon | | | | | = |&#8826; scope |&#8826; scope<br/>&#8614; **deadline**<br/>&#8626; goto |
| R8 | | | | | | | |&#8911; scope | = | |
| R9 | | | | | | | |&#8911; scope<br/>&#8615; **deadline**<br/>&#8624; goto | | = |