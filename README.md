# twitch-embed
Make Twitch's interactive embed video script available on npm.  I needed it available on a project and figured other's might too.

## Implementation ##
This isn't the most ideal implementation.  The script still pollutes the `window` and the functions/objects are NOT able to be imported. i.e. `import { Player } from 'twitch-embed'`.  To enable this Twitch would have to provide its source and make a few modifications.

lib/v1.js is literally just http://player.twitch.tv/js/embed/v1.js

## Modifications ##
I did have to make a change to the source.  I pretty printed it, and hard coded the base of the Twitch video requests to `'http://player.twitch.tv/js/embed/v1.js'` you can see the change at line 320.

## Usage ##

    npm i --save twitch-embed
    import 'twitch-embed';

## eslint ##
You may need to disable the eslint error for global variable usage if you are referencing the Twitch variable without window. (Twitch vs window.Twitch)

	//.eslintrc
	{
      "globals": {
        "Twitch": false
      }
	}


See Twitch's Video Embed page for more information.
[https://github.com/justintv/Twitch-API/blob/master/embed-video.md](https://github.com/justintv/Twitch-API/blob/master/embed-video.md "Embedding Twitch Live Streams & Videos")

## React class ES6 Example ##

	import React from 'react';
	import 'twitch-embed';

	export default class TwitchVideoEmbed extends React.Component {
		constructor(props) {
			super(props);
			this.player = null;
			this.state = {
				id: null
			};
		}

		static propTypes = {
			channel: React.PropTypes.string,
			video: React.PropTypes.string,
			play: React.PropTypes.bool
		};

		componentWillMount() {
			this.setId();
		}

		componentDidMount() {
			this.setPlayer();
		}

		componentDidUpdate() {
			this.setPlayer();
		}

		componentWillReceiveProps(nextProps) {
			this.setId();
			this.setPlayer();

			//can check for props and call player functions here
		}

		setId() {
			if (!this.state.id) {
				if (this.props.channel) {
					this.channel = true;
					this.setState({
						id: `twitch-${this.props.channel}`
					});
				}
				if (this.props.video) {
					this.channel = false;
					this.setState({
						id: `twitch-${this.props.video}`
					});
				}
			}
		}

		setPlayer() {
			if (!this.player) {
				const options = {};
				if (this.channel) {
					options.channel = this.props.channel;
				} else {
					options.video = this.props.video;
				}
				if (typeof window !== 'undefined' && window.Twitch) {
					this.player = new window.Twitch.Player(this.state.id, options);
				}
			}
		}

		render() {
			return (
				<div id={this.state.id || ''} className="twitch-video-embed"></div>
			);
		}
	}

