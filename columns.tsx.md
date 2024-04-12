Vamos definir nossa coluna

```tsx
"use client"

import { ColumnDef } from "@tanstack/react-table"

// Esse tipo é usado para definir as formas de nossos dados.
// You can use a Zod schema here if you want.
export type Payment = {
  id: string
  amount: number
  status: "pendente" | "processando" | "sucesso" | "falho"
  email: string
}

export const columns: ColumnDef<Payment>[] = [
  {
    accessorKey: "status",
    header: "Status",
  },
  {
    accessorKey: "email",
    header: "Email",
  },
  {
    accessorKey: "valor",
    header: "valor",
  },
]

```

Aqui definimos os cabeçalhos de nossa tabela