.PHONY: new open close

new:
	@read -p "Input new slide name: " NEW_DIR; echo $${NEW_DIR};\
	git checkout template && git pull && git checkout -b $${NEW_DIR};\
	cd ..
	cp -r template $${NEW_DIR};\
        echo "https://sotets.uk/slides/$${NEW_DIR}" > $${NEW_DIR}/README.md;\
	git add $${NEW_DIR}/*;\
	git commit -m "New $${NEW_DIR} (auto generation by new script)";\
	git push --set-upstream origin $${NEW_DIR}
	cd $${NEW_DIR}

open:
	@git branch | grep \* | awk '{print $2}' | cd
	@python -m SimpleHTTPServer 8000&
	@sleep 1
	@open http://localhost:8000

close:
	@ps -alx | grep "python -m SimpleHTTPServer" | grep -v grep | awk '{print "kill -9", $$2}' | sh
