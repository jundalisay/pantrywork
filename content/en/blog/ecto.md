+++
title = "Ecto"
subtitle = ""
banner = "/img/phx.jpg"
description = "Here are a ecto commands"
tags = ['Phoenix']
date = 2022-04-15
+++

post = MyRepo.get!(Post, 42)
post = Ecto.Changeset.change post, title: "New title"
case MyRepo.update post do
  {:ok, struct}       -> # Updated with success
  {:error, changeset} -> # Something went wrong
end

Repo.get_by(Chat, id: chat_id)
|> Ecto.Changeset.change(%{visible: true}) 
|> Repo.update()